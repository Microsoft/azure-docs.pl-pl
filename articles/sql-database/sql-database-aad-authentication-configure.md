---
title: Konfigurowanie uwierzytelniania usługi Azure Active Directory
description: Dowiedz się, jak nawiązać połączenie z usługą SQL Database, wystąpieniem zarządzanym i analizą usługi Azure Synapse przy użyciu uwierzytelniania Azure Active Directory — po skonfigurowaniu usługi Azure AD.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: azure-synapse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
ms.date: 03/27/2020
ms.openlocfilehash: 0e244ea185011bbb7d9f0facad399bb9b577bbc2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80419886"
---
# <a name="configure-and-manage-azure-active-directory-authentication-with-sql"></a>Configure and manage Azure Active Directory authentication with SQL (Konfigurowanie uwierzytelniania w usłudze Azure Active Directory i zarządzanie nim przy użyciu języka SQL)

W tym artykule opisano sposób tworzenia i wypełniania usługi Azure AD, a następnie używania usługi Azure AD z usługą Azure [SQL Database (baza danych SQL)](sql-database-technical-overview.md), [wystąpieniem zarządzanym](sql-database-managed-instance.md)i [usługą Azure Synapse Analytics (dawniej Azure SQL Data Warehouse)](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Aby zapoznać się z omówieniem, zobacz [Azure Active Directory Authentication](sql-database-aad-authentication.md).

> [!NOTE]
> Ten artykuł ma zastosowanie do programu Azure SQL Server i zarówno do SQL Database, jak i do usługi Azure Synapse. Dla uproszczenia SQL Database jest używany podczas odwoływania się do SQL Database i usługi Azure Synapse.

> [!IMPORTANT]  
> Nawiązywanie połączenia z usługą SQL Server działającej na maszynie wirtualnej platformy Azure nie jest obsługiwane przy użyciu konta Azure Active Directory. Zamiast tego użyj konta Active Directory domeny.

## <a name="azure-ad-authentication-methods"></a>Metody uwierzytelniania usługi Azure AD

Uwierzytelnianie usługi Azure AD obsługuje następujące metody uwierzytelniania:

- Tożsamości tylko w chmurze usługi Azure AD
- Tożsamości hybrydowe usługi Azure AD, które obsługują:
  - Uwierzytelnianie w chmurze z dwoma opcjami połączonymi z bezproblemowym logowaniem jednokrotnym (SSO)
    - Uwierzytelnianie skrótu hasła usługi Azure AD
    - Uwierzytelnianie przekazywane przez usługę Azure AD
  - Uwierzytelnianie federacyjne

Aby uzyskać więcej informacji na temat metod uwierzytelniania usługi Azure AD, które można wybrać, zobacz artykuł:
- [Wybierz właściwą metodę uwierzytelniania dla Azure Active Directory rozwiązanie do tworzenia tożsamości hybrydowej](../active-directory/hybrid/choose-ad-authn.md)

Aby uzyskać więcej informacji na temat tożsamości hybrydowych usługi Azure AD, instalacji i synchronizacji, zobacz następujące artykuły:

- Uwierzytelnianie skrótu hasła — [implementowanie synchronizacji skrótów haseł z synchronizacją Azure AD Connect](../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)
- Uwierzytelnianie przekazywane — [Azure Active Directory uwierzytelniania przekazywanego](../active-directory/hybrid/how-to-connect-pta-quick-start.md)
- Uwierzytelnianie federacyjne — [wdrażanie Active Directory Federation Services na platformie Azure](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs) i [Azure AD Connect i w Federacji](../active-directory/hybrid/how-to-connect-fed-whatis.md)

Wszystkie powyższe metody uwierzytelniania są obsługiwane w przypadku usługi SQL DB (pojedynczej bazy danych i pul baz danych), wystąpienia zarządzanego i platformy Azure Synapse.

## <a name="create-and-populate-an-azure-ad"></a>Tworzenie i wypełnianie usługi Azure AD

Utwórz usługę Azure AD i wypełnij ją użytkownikami i grupami. Usługa Azure AD może być początkową domeną zarządzaną usługi Azure AD. Usługa Azure AD może być również lokalnym Active Directory Domain Servicesm federacyjnym z usługą Azure AD.

Aby uzyskać więcej informacji, zobacz tematy [Integrating your on-premises identities with Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md) (Integrowanie tożsamości lokalnych z usługą Azure Active Directory), [Dodawanie niestandardowej nazwy domeny do usługi Azure AD](../active-directory/active-directory-domains-add-azure-portal.md), [Microsoft Azure now supports federation with Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/) (Platforma Microsoft Azure obsługuje teraz federację z usługą Windows Servder Active Directory), [Administering your Azure AD directory](../active-directory/fundamentals/active-directory-administer.md) (Administrowanie katalogiem usługi Azure AD), [Manage Azure AD using Windows PowerShell](/powershell/azure/overview) (Zarządzanie usługą Azure AD przy użyciu programu Windows PowerShell) i [Hybrid Identity Required Ports and Protocols](../active-directory/hybrid/reference-connect-ports.md) (Wymagane porty i protokoły tożsamości hybrydowych).

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Kojarzenie lub dodawanie subskrypcji platformy Azure do usługi Azure Active Directory

1. Skojarz swoją subskrypcję platformy Azure z Azure Active Directory przez utworzenie katalogu jako zaufanego katalogu subskrypcji platformy Azure, w którym znajduje się baza danych. Aby uzyskać szczegółowe informacje, zobacz [jak subskrypcje platformy Azure są kojarzone z usługą Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

2. Użyj przełącznika katalogu w Azure Portal, aby przełączyć się na subskrypcję skojarzoną z domeną.

   > [!IMPORTANT]
   > Każda subskrypcja platformy Azure jest połączona relacją zaufania z wystąpieniem usługi Azure AD. Oznacza to, że subskrypcja ufa katalogowi na potrzeby uwierzytelniania użytkowników, usług i urządzeń. Wiele subskrypcji może ufać temu samemu katalogowi, ale dana subskrypcja może ufać tylko jednemu katalogowi. Relacja zaufania między subskrypcją a katalogiem różni się od relacji subskrypcji z wszystkimi innymi zasobami na platformie Azure (witrynami sieci Web, bazami danych itd.), które przypominają bardziej podrzędne zasoby subskrypcji. Jeśli subskrypcja wygaśnie, dostęp do innych zasobów skojarzonych z subskrypcją również nie będzie możliwy. Lecz katalog pozostanie na platformie Azure i będzie można skojarzyć z nim inną subskrypcję oraz kontynuować zarządzanie użytkownikami w katalogu. Aby uzyskać więcej informacji o zasobach, zobacz [Opis dostępu do zasobów na platformie Azure](../active-directory/active-directory-b2b-admin-add-users.md). Aby dowiedzieć się więcej na temat relacji zaufania [, zobacz Jak skojarzyć lub dodać subskrypcję platformy Azure do Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="create-an-azure-ad-administrator-for-azure-sql-server"></a>Tworzenie administratora usługi Azure AD dla programu Azure SQL Server

Każdy serwer SQL platformy Azure (który obsługuje SQL Database lub Azure Synapse) jest uruchamiany przy użyciu jednego konta administratora serwera, które jest administratorem całego serwera SQL Azure. Należy utworzyć drugiego administratora SQL Server, który jest kontem usługi Azure AD. Ten podmiot zabezpieczeń jest tworzony jako użytkownik zawartej bazy danych w bazie danych Master. Jako administratorzy konta administratorów serwera są członkami roli **db_owner** w każdej bazie danych użytkownika, a następnie wprowadź każdą bazę danych użytkownika jako użytkownika **dbo** . Więcej informacji o kontach administratorów serwera znajduje się [w temacie Zarządzanie bazami danych i nazwami logowania w Azure SQL Database](sql-database-manage-logins.md).

W przypadku korzystania z Azure Active Directory z replikacją geograficzną administrator Azure Active Directory musi być skonfigurowany zarówno dla serwera podstawowego, jak i pomocniczego. Jeśli serwer nie ma Azure Active Directory administrator, wówczas Azure Active Directory logowania i użytkownicy otrzymają komunikat o błędzie "nie można nawiązać połączenia" z serwerem.

> [!NOTE]
> Użytkownicy, którzy nie korzystają z konta usługi Azure AD (w tym konta administratora programu Azure SQL Server), nie mogą tworzyć użytkowników opartych na usłudze Azure AD, ponieważ nie mają uprawnień do weryfikowania proponowanych użytkowników bazy danych za pomocą usługi Azure AD.

## <a name="provision-an-azure-active-directory-administrator-for-your-managed-instance"></a>Inicjowanie obsługi administracyjnej Azure Active Directory administratora dla wystąpienia zarządzanego

> [!IMPORTANT]
> Wykonaj te czynności tylko w przypadku aprowizacji wystąpienia zarządzanego. Tę operację można wykonać tylko przez administratora globalnego/firmy lub administratora ról uprzywilejowanych w usłudze Azure AD. Poniższe kroki opisują proces przyznawania uprawnień użytkownikom mającym różne uprawnienia w katalogu.

> [!NOTE]
> W przypadku administratorów usługi Azure AD dla mnie utworzonych przed rozpoczęciem korzystania z systemu, ale kontynuuje posting, nie ma żadnej funkcjonalnej zmiany w istniejącym zachowaniu. Aby uzyskać więcej informacji, zapoznaj się z sekcją [nowe funkcje administratora usługi Azure AD dla elementu mi](#new-azure-ad-admin-functionality-for-mi) , aby uzyskać więcej informacji.

Wystąpienie zarządzane musi mieć uprawnienia do odczytu usługi Azure AD, aby pomyślnie wykonać zadania, takie jak uwierzytelnianie użytkowników za pomocą przynależności do grupy zabezpieczeń lub tworzenie nowych użytkowników. Aby to umożliwić, należy przyznać uprawnienia do wystąpienia zarządzanego w celu odczytania usługi Azure AD. Istnieją dwa sposoby wykonania tej czynności: z poziomu portalu i programu PowerShell. Obie metody są opisane poniżej.

1. W witrynie Azure Portal w prawym górnym rogu wybierz swoje połączenia, aby wyświetlić listę rozwijaną możliwych usług Active Directory.

2. Wybierz odpowiednią usługę Active Directory jako domyślną usługę Azure AD.

   Ten krok łączy subskrypcję skojarzoną z Active Directoryą z wystąpieniem zarządzanym, upewniając się, że ta sama subskrypcja jest używana dla usługi Azure AD i wystąpienia zarządzanego.

3. Przejdź do wystąpienia zarządzanego i wybierz konto, którego chcesz używać do integracji z usługą Azure AD.

   ![AAD](./media/sql-database-aad-authentication/aad.png)

4. Wybierz transparent w górnej części strony administratora Active Directory i Udziel uprawnienia bieżącemu użytkownikowi. Jeśli użytkownik jest zalogowany jako Administrator globalny/firmowy w usłudze Azure AD, można wykonać tę czynność z poziomu Azure Portal lub przy użyciu programu PowerShell z poniższym skryptem.

    ![Przyznawanie uprawnień — Portal](./media/sql-database-aad-authentication/grant-permissions.png)

    ```powershell
    # Gives Azure Active Directory read permission to a Service Principal representing the managed instance.
    # Can be executed only by a "Company Administrator", "Global Administrator", or "Privileged Role Administrator" type of user.

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

    # Get service principal for managed instance
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

5. Po pomyślnym zakończeniu operacji następujące powiadomienie zostanie wyświetlone w prawym górnym rogu:

    ![powodzenie](./media/sql-database-aad-authentication/success.png)

6. Teraz możesz wybrać administratora usługi Azure AD dla wystąpienia zarządzanego. W tym celu na stronie Administrator Active Directory wybierz pozycję **Ustaw polecenie administracyjne** .

    ![Ustawianie — administrator](./media/sql-database-aad-authentication/set-admin.png)

7. Na stronie administrator usługi AAD Wyszukaj użytkownika, wybierz użytkownika lub grupę, który ma być administratorem, a następnie wybierz pozycję **Wybierz**.

   Na stronie Administrator Active Directory są wyświetlane wszystkie elementy członkowskie i grupy Active Directory. Nie można wybrać użytkowników lub grup, które są wyszarzone, ponieważ nie są one obsługiwane jako Administratorzy usługi Azure AD. Zapoznaj się z listą obsługiwanych administratorów w [funkcjach i ograniczeniach usługi Azure AD](sql-database-aad-authentication.md#azure-ad-features-and-limitations). Kontrola dostępu oparta na rolach (RBAC) ma zastosowanie tylko do Azure Portal i nie jest propagowana do SQL Server.

    ![Dodaj administratora Azure Active Directory](./media/sql-database-aad-authentication/add-azure-active-directory-admin.png)

8. W górnej części strony Administrator Active Directory wybierz pozycję **Zapisz**.

    ![zapisywanie](./media/sql-database-aad-authentication/save.png)

    Proces zmiany administratora może potrwać kilka minut. Nowy administrator pojawi się w polu Administrator usługi Active Directory.

Po zainicjowaniu obsługi administracyjnej administratora usługi Azure AD dla wystąpienia zarządzanego można rozpocząć tworzenie podmiotów zabezpieczeń serwera usługi Azure AD przy użyciu składni <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">tworzenia nazwy logowania</a> . Aby uzyskać więcej informacji, zobacz [Omówienie wystąpienia zarządzanego](sql-database-managed-instance.md#azure-active-directory-integration).

> [!TIP]
> Aby później usunąć administratora, w górnej części strony Administrator Active Directory wybierz pozycję **Usuń administratora**, a następnie wybierz pozycję **Zapisz**.

### <a name="new-azure-ad-admin-functionality-for-mi"></a>Nowe funkcje administratora usługi Azure AD dla programu MI

Poniższa tabela zawiera podsumowanie funkcji administratora logowania do usługi Azure AD w wersji zapoznawczej dla mnie, a następnie nowe funkcje dostarczane z usługą Azure AD na potrzeby logowania.

| Administrator logowania usługi Azure AD dla mnie w publicznej wersji zapoznawczej | Funkcja GA dla administratora usługi Azure AD dla mnie |
| --- | ---|
| Zachowuje się w podobny sposób jak administrator usługi Azure AD dla SQL Database, co umożliwia uwierzytelnianie usługi Azure AD, ale administrator usługi Azure AD nie może utworzyć usługi Azure AD lub logowania SQL w bazie danych Master dla mnie. | Administrator usługi Azure AD ma uprawnienie sysadmin i może tworzyć identyfikatory logowania w usłudze AAD i SQL w bazie danych Master dla programu MI. |
| Nie występuje w widoku sys. server_principals | Znajduje się w widoku sys. server_principals |
| Zezwala indywidualnym użytkownikom gościa usługi Azure AD na skonfigurowanie usługi Azure AD administrator dla programu MI. Aby uzyskać więcej informacji, zobacz [dodawanie Azure Active Directory użytkowników współpracy B2B w Azure Portal](../active-directory/b2b/add-users-administrator.md). | Wymaga utworzenia grupy usługi Azure AD z użytkownikami Gości jako członkami, aby skonfigurować tę grupę jako administratora usługi Azure AD dla MI. Aby uzyskać więcej informacji, zobacz temat [Obsługa usługi Azure AD Business dla firm](sql-database-ssms-mfa-authentication.md#azure-ad-business-to-business-support). |

Jako najlepsze rozwiązanie dla istniejących administratorów usługi Azure AD dla mnie utworzonych przed rozpowszechną i nadal działające posting, zresetuj administratora usługi Azure AD przy użyciu opcji Azure Portal "Usuń administratora" i "Ustaw administratora" dla tego samego użytkownika lub grupy usługi Azure AD.

### <a name="known-issues-with-the-azure-ad-login-ga-for-mi"></a>Znane problemy związane z logowaniem do usługi Azure AD GA dla mnie

- Jeśli w bazie danych Master istnieje identyfikator logowania usługi Azure AD dla mnie, utworzony za pomocą polecenia `CREATE LOGIN [myaadaccount] FROM EXTERNAL PROVIDER`T-SQL nie można skonfigurować jako administratora usługi Azure AD dla mi. Wystąpił błąd podczas konfigurowania logowania jako administrator usługi Azure AD przy użyciu poleceń Azure Portal, PowerShell lub interfejsu wiersza polecenia, aby utworzyć identyfikator logowania usługi Azure AD.
  - Aby można było utworzyć konto jako administrator usługi Azure AD, należy `DROP LOGIN [myaadaccount]`porzucić nazwę logowania w bazie danych Master przy użyciu polecenia.
  - Skonfiguruj konto administratora usługi Azure AD w Azure Portal po `DROP LOGIN` pomyślnym zakończeniu. 
  - Jeśli nie możesz skonfigurować konta administratora usługi Azure AD, zapoznaj się z główną bazą danych wystąpienia zarządzanego. Użyj następującego polecenia:`SELECT * FROM sys.server_principals`
  - Skonfigurowanie administratora usługi Azure AD dla programu MI spowoduje automatyczne utworzenie nazwy logowania w bazie danych Master dla tego konta. Usunięcie administratora usługi Azure AD spowoduje automatyczne porzucenie nazwy logowania z bazy danych Master.

- Indywidualni użytkownicy Gości usługi Azure AD nie są obsługiwani jako Administratorzy usługi Azure AD. Użytkownicy-Goście muszą być częścią grupy usługi Azure AD, aby można ją było skonfigurować jako administratora usługi Azure AD. Obecnie blok Azure Portal nie jest wyszarzony dla użytkowników-Gości dla innej usługi Azure AD, co pozwala użytkownikom na kontynuowanie instalacji administratora. Zapisanie użytkowników-Gości jako administrator usługi Azure AD spowoduje niepowodzenie instalacji.
  - Jeśli chcesz, aby użytkownik-Gość był administratorem usługi Azure AD, Uwzględnij użytkownika-gościa w grupie usługi Azure AD i ustaw tę grupę jako administratora usługi Azure AD.

### <a name="powershell-for-sql-managed-instance"></a>PowerShell dla wystąpienia zarządzanego SQL

# <a name="powershell"></a>[Narzędzia](#tab/azure-powershell)

Aby uruchomić polecenia cmdlet programu PowerShell, należy zainstalować i uruchomić Azure PowerShell. Aby uzyskać szczegółowe informacje, zobacz temat [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).

> [!IMPORTANT]
> Moduł programu PowerShell Azure Resource Manager (RM) jest nadal obsługiwany przez Azure SQL Database, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. Moduł AzureRM będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020.  Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne. Aby uzyskać więcej informacji o zgodności, zobacz [wprowadzenie do nowego Azure PowerShell AZ module](/powershell/azure/new-azureps-module-az).

Aby zainicjować obsługę administracyjną administratora usługi Azure AD, wykonaj następujące polecenia Azure PowerShell:

- Connect-AzAccount
- SELECT-AzSubscription

Polecenia cmdlet służące do aprowizacji i zarządzania usługą Azure AD Admin dla wystąpienia zarządzanego SQL:

| Nazwa polecenia cmdlet | Opis |
| --- | --- |
| [Set-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) |Inicjuje usługę Azure AD administrator dla wystąpienia zarządzanego SQL w bieżącej subskrypcji. (Musi być z bieżącej subskrypcji)|
| [Remove-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlinstanceactivedirectoryadministrator) |Usuwa administratora usługi Azure AD dla wystąpienia zarządzanego SQL w bieżącej subskrypcji. |
| [Get-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlinstanceactivedirectoryadministrator) |Zwraca informacje o administratorze usługi Azure AD dla wystąpienia zarządzanego SQL w bieżącej subskrypcji.|

Następujące polecenie pobiera informacje o administratorze usługi Azure AD dla wystąpienia zarządzanego o nazwie ManagedInstance01, które jest skojarzone z grupą zasobów o nazwie ResourceGroup01.

```powershell
Get-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01"
```

Następujące polecenie inicjuje grupę administratorów usługi Azure AD o nazwie przetwarzający dla wystąpienia zarządzanego o nazwie ManagedInstance01. Ten serwer jest skojarzony z grupą zasobów ResourceGroup01.

```powershell
Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01" -DisplayName "DBAs" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353b"
```

Następujące polecenie usuwa administratora usługi Azure AD dla wystąpienia zarządzanego o nazwie ManagedInstanceName01 skojarzone z grupą zasobów ResourceGroup01.

```powershell
Remove-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstanceName01" -Confirm -PassThru
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Możesz również zainicjować obsługę administracyjną usługi Azure AD dla wystąpienia zarządzanego SQL przez wywołanie następujących poleceń interfejsu wiersza polecenia:

| Polecenie | Opis |
| --- | --- |
|[AZ SQL mi AD-administrator Create](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-create) | Inicjuje obsługę administracyjną Azure Active Directory dla wystąpienia zarządzanego SQL. (Musi być z bieżącej subskrypcji) |
|[AZ SQL mi AD-administrator Delete](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-delete) | Usuwa Azure Active Directory administratora dla wystąpienia zarządzanego SQL. |
|[AZ SQL mi AD — Lista administratorów](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-list) | Zwraca informacje o Azure Active Directory administrator aktualnie skonfigurowany do wystąpienia zarządzanego SQL. |
|[AZ SQL mi AD-administrator Update](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-update) | Aktualizuje Active Directory administratora dla wystąpienia zarządzanego SQL. |

Aby uzyskać więcej informacji na temat poleceń interfejsu wiersza polecenia, zobacz [AZ SQL mi](/cli/azure/sql/mi).

* * *

## <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server"></a>Aprowizowanie administratora usługi Azure Active Directory dla serwera usługi Azure SQL Database

> [!IMPORTANT]
> Wykonaj te czynności tylko w przypadku aprowizacji serwera Azure SQL Database lub analizy usługi Azure Synapse.

W poniższych dwóch procedurach pokazano, jak zainicjować obsługę administracyjną Azure Active Directory administratorem programu Azure SQL Server w Azure Portal i przy użyciu programu PowerShell.

### <a name="azure-portal"></a>Azure Portal

1. W witrynie [Azure Portal](https://portal.azure.com/) w prawym górnym rogu wybierz swoje połączenia, aby wyświetlić listę rozwijaną możliwych usług Active Directory. Wybierz odpowiednią usługę Active Directory jako domyślną usługę Azure AD. W tym kroku powiązana z subskrypcją usługa Active Directory zostaje połączona z serwerem Azure SQL. Dzięki temu mamy pewność, że ta sama subskrypcja jest używana zarówno przez usługę Azure AD, jak i SQL Server. (Serwer SQL platformy Azure może obsługiwać Azure SQL Database lub usługę Azure Synapse).

    ![Wybór usługi AD][8]

2. Wyszukaj i wybierz pozycję **SQL Server**.

    ![Wyszukaj i wybierz pozycję Serwery SQL](media/sql-database-aad-authentication/search-for-and-select-sql-servers.png)

    >[!NOTE]
    > Na tej stronie przed wybraniem opcji **serwery SQL**możesz wybrać **gwiazdkę** obok nazwy, aby dodać do *ulubionych* kategorię, i Dodaj **serwery SQL** na lewym pasku nawigacyjnym.

3. Na stronie **SQL Server** wybierz pozycję **administrator Active Directory**.

4. Na stronie **administrator Active Directory** wybierz pozycję **Ustaw administratora**.

    ![Serwery SQL ustawione jako administrator Active Directory](./media/sql-database-aad-authentication/sql-servers-set-active-directory-admin.png)  

5. Na stronie **Dodawanie administratora** wyszukaj użytkownika, wybierz użytkownika lub grupę do pełnienia funkcji administratora, a następnie kliknij opcję **Wybierz**. (Na stronie administratora usługi Active Directory wyświetlono wszystkich członków i grupy danej usługi Active Directory). Nie można wybrać wyszarzonych użytkowników lub grup, ponieważ nie są oni obsługiwani jako administratorzy usługi Azure AD. (Zobacz listę obsługiwanych administratorów w sekcji funkcje usługi **Azure AD i ograniczenia** dotyczące [uwierzytelniania przy użyciu usługi Azure Active Directory Authentication SQL Database lub Azure Synapse](sql-database-aad-authentication.md)). Kontrola dostępu oparta na rolach (RBAC) dotyczy tylko portalu i nie jest propagowana do SQL Server.

    ![Wybierz administratora Azure Active Directory](./media/sql-database-aad-authentication/select-azure-active-directory-admin.png)  

6. W górnej części strony **Administrator usługi Active Directory** wybierz opcję **ZAPISZ**.

    ![zapisywanie administratora](./media/sql-database-aad-authentication/save-admin.png)

Proces zmiany administratora może potrwać kilka minut. Nowy administrator pojawi się w polu **Administrator usługi Active Directory**.

   > [!NOTE]
   > Podczas konfigurowania administratora usługi Azure AD nowa nazwa administratora (użytkownika lub grupy) nie może już istnieć w wirtualnej bazie danych master jako nazwa użytkownika używana do uwierzytelniania na serwerze SQL. Jeśli już istnieje, konfiguracja administratora usługi Azure AD nie powiedzie się. Tworzenie administratora zostanie cofnięte i wyświetli się komunikat, że administrator z taką nazwą już istnieje. Ponieważ nazwa użytkownika do uwierzytelniania na serwerze SQL nie jest częścią usługi Azure AD, próba połączenia się z serwerem za pomocą uwierzytelniania z usługi AD nie powiedzie się.

Aby później usunąć administratora, w górnej części strony **administrator Active Directory** wybierz pozycję **Usuń administratora**, a następnie wybierz pozycję **Zapisz**.

### <a name="powershell-for-azure-sql-database-and-azure-synapse"></a>Program PowerShell dla Azure SQL Database i Azure Synapse

# <a name="powershell"></a>[Narzędzia](#tab/azure-powershell)

Aby uruchomić polecenia cmdlet programu PowerShell, należy zainstalować i uruchomić Azure PowerShell. Aby uzyskać szczegółowe informacje, zobacz temat [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview). Aby zainicjować obsługę administracyjną administratora usługi Azure AD, wykonaj następujące polecenia Azure PowerShell:

- Connect-AzAccount
- SELECT-AzSubscription

Polecenia cmdlet służące do aprowizacji i zarządzania administratorem usługi Azure AD dla Azure SQL Database i usługi Azure Synapse:

| Nazwa polecenia cmdlet | Opis |
| --- | --- |
| [Set-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlserveractivedirectoryadministrator) |Inicjuje Azure Active Directory administratorem usługi Azure SQL Server lub Azure Synapse. (Musi być z bieżącej subskrypcji) |
| [Remove-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlserveractivedirectoryadministrator) |Usuwa administratora Azure Active Directory dla programu Azure SQL Server lub Azure Synapse. |
| [Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator) |Zwraca informacje o administratorze Azure Active Directory aktualnie skonfigurowanym dla serwera SQL Azure lub usługi Azure Synapse. |

Aby uzyskać więcej informacji na temat każdego z tych poleceń, użyj polecenia programu PowerShell get-help. Na przykład `get-help Set-AzSqlServerActiveDirectoryAdministrator`.

Poniższy skrypt inicjuje grupę administratorów usługi Azure AD o nazwie **DBA_Group** (identyfikator `40b79501-b343-44ed-9ce7-da4c8cc7353f`obiektu) dla serwera **demo_server** w grupie zasobów o nazwie **Grupa-23**:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" -DisplayName "DBA_Group"
```

Parametr wejściowy **DisplayName** akceptuje nazwę wyświetlaną usługi Azure AD lub główną nazwę użytkownika. Na przykład ``DisplayName="John Smith"`` i ``DisplayName="johns@contoso.com"``. W przypadku grup usługi Azure AD jest obsługiwana tylko nazwa wyświetlana usługi Azure AD.

> [!NOTE]
> Polecenie ```Set-AzSqlServerActiveDirectoryAdministrator``` Azure PowerShell nie uniemożliwia aprowizacji administratorów usługi Azure AD dla nieobsługiwanych użytkowników. Nieobsługiwany użytkownik może zostać zainicjowany, ale nie może nawiązać połączenia z bazą danych.

Poniższy przykład używa opcjonalnego **identyfikatora objectid**:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" `
    -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> Identyfikator **objectid** usługi Azure AD jest wymagany, gdy **Właściwość DisplayName** nie jest unikatowa. Aby pobrać wartości **objectid** i **DisplayName** , użyj Active Directory sekcji klasyczny portal Azure i Wyświetl właściwości użytkownika lub grupy.

Poniższy przykład zwraca informacje dotyczące bieżącego administratora usługi Azure AD dla programu Azure SQL Server:

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
|[AZ SQL Server AD-administrator Create](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-create) | Inicjuje Azure Active Directory administratorem usługi Azure SQL Server lub Azure Synapse. (Musi być z bieżącej subskrypcji) |
|[AZ SQL Server AD-administrator Delete](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-delete) | Usuwa administratora Azure Active Directory dla programu Azure SQL Server lub Azure Synapse. |
|[AZ SQL Server AD-administrator list](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-list) | Zwraca informacje o administratorze Azure Active Directory aktualnie skonfigurowanym dla serwera SQL Azure lub usługi Azure Synapse. |
|[AZ SQL Server AD-administrator Update](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-update) | Aktualizuje Active Directory administratora usługi Azure SQL Server lub Azure Synapse. |

Aby uzyskać więcej informacji na temat poleceń interfejsu wiersza polecenia, zobacz [AZ SQL Server](/cli/azure/sql/server).

* * *

> [!NOTE]
> Możesz również udostępnić administratorowi Azure Active Directory za pomocą interfejsów API REST. Aby uzyskać więcej informacji, zobacz informacje o [interfejsie API REST usługi Service Management oraz operacje Azure SQL Database operacji dla Azure SQL Database](/rest/api/sql/)

## <a name="configure-your-client-computers"></a>Konfigurowanie komputerów klienckich

Na wszystkich komputerach klienckich, z których aplikacje lub użytkownicy łączą się z Azure SQL Database lub Azure Synapse przy użyciu tożsamości usługi Azure AD, należy zainstalować następujące oprogramowanie:

- .NET Framework 4,6 lub nowszy z [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx).
- Azure Active Directory bibliotekę uwierzytelniania dla SQL Server (*ADAL. DLL*). Poniżej znajdują się linki pobierania umożliwiające zainstalowanie najnowszego sterownika programu SSMS, ODBC i OLE DB zawierającego bibliotekę *ADAL. Biblioteka DLL* .
    1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
    1. [Sterownik ODBC 17 dla SQL Server](https://www.microsoft.com/download/details.aspx?id=56567)
    1. [OLE DB Driver 18 dla SQL Server](https://www.microsoft.com/download/details.aspx?id=56730)

Wymagania te można spełnić w następujący sposób:

- Zainstalowanie najnowszej wersji [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) lub [narzędzi danych SQL Server](/sql/ssdt/download-sql-server-data-tools-ssdt) spełnia wymagania .NET Framework 4,6.
    - Program SSMS instaluje wersję x86 *biblioteki ADAL. Biblioteka DLL*.
    - SSDT instaluje wersję amd64 *biblioteki ADAL. Biblioteka DLL*.
    - Najnowsza wersja programu Visual Studio z programu [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) jest zgodna z wymaganiami dotyczącymi .NET Framework 4,6, ale nie instaluje wymaganej wersji amd64 *biblioteki ADAL. Biblioteka DLL*.

## <a name="create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>Utwórz użytkowników zawartej bazy danych w bazie danych zamapowanej na tożsamości usługi Azure AD

> [!IMPORTANT]
> Wystąpienie zarządzane obsługuje teraz podmioty zabezpieczeń serwera usługi Azure AD, które umożliwiają tworzenie logowań z użytkowników, grup lub aplikacji usługi Azure AD. Podmioty zabezpieczeń serwera usługi Azure AD umożliwiają uwierzytelnianie w zarządzanym wystąpieniu bez konieczności tworzenia użytkowników bazy danych jako zawartych użytkowników bazy danych. Aby uzyskać więcej informacji, zobacz [Omówienie wystąpienia zarządzanego](sql-database-managed-instance.md#azure-active-directory-integration). Aby zapoznać się ze składnią tworzenia podmiotów zabezpieczeń serwera usługi Azure AD (logowania), zobacz <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Tworzenie nazwy logowania</a>.

Uwierzytelnianie w usłudze Azure Active Directory wymaga utworzenia użytkowników bazy danych jako użytkowników zawartej bazy danych. Użytkownik zawartej bazy danych oparty na tożsamości w usłudze Azure AD to użytkownik bazy danych, który nie ma loginu w bazie danych master i jest zamapowany na tożsamość w katalogu Azure AD skojarzonym z bazą danych. Tożsamość w usłudze Azure AD może być indywidualnym kontem użytkownika lub grupą. Aby uzyskać więcej informacji na temat użytkowników zawartej bazy danych, patrz [Contained Database Users - Making Your Database Portable](https://msdn.microsoft.com/library/ff929188.aspx) (Użytkownicy zawartych baz danych — tworzenie przenośnej bazy danych).

> [!NOTE]
> Użytkowników bazy danych (z wyjątkiem administratorów) nie można tworzyć za pośrednictwem witryny Azure Portal. Role RBAC nie są propagowane do SQL Server, SQL Database lub Azure Synapse. Role RBAC na platformie Azure są używane do zarządzania zasobami platformy Azure i nie mają zastosowania do uprawnień do bazy danych. Na przykład rola **współautor SQL Server** nie udziela dostępu do połączenia z SQL Database lub Azure Synapse. Uprawnienie dostępu należy nadać bezpośrednio w bazie danych za pomocą instrukcji języka Transact-SQL.

> [!WARNING]
> Znaki specjalne, takie jak dwukropek `:` lub ampersand `&` uwzględnione w nazwie użytkownika w instrukcjach języka T-SQL CREATE LOGIN  i CREATE USER nie są obsługiwane.

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
> To polecenie wymaga, aby program SQL Server w imieniu zalogowanego użytkownika miał dostęp do usługi Azure AD ("dostawca zewnętrzny"). Czasami powstają sytuacje, w których usługa Azure AD zwróci wyjątek z powrotem do bazy danych SQL. W takich przypadkach użytkownik zobaczy błąd SQL 33134, który powinien zawierać komunikat o błędzie dotyczący usługi AAD. W większości przypadków błąd oznacza odmowa dostępu lub że użytkownik musi zarejestrować się w usłudze MFA w celu uzyskania dostępu do zasobu lub uzyskać dostęp między aplikacjami pierwszej firmy, muszą być obsługiwane za pośrednictwem autoryzacji wstępnej. W pierwszych dwóch przypadkach problem jest zwykle spowodowany przez zasady dostępu warunkowego, które są ustawione w dzierżawie usługi AAD użytkownika: uniemożliwia użytkownikowi dostęp do zewnętrznego dostawcy. Aktualizacja zasad urzędu certyfikacji w celu zezwolenia na dostęp do aplikacji "00000002-0000-0000-C000-000000000000" (Identyfikator aplikacji usługi AAD interfejs API programu Graph) powinna rozwiązać ten problem. W przypadku, gdy błąd mówi dostępu między aplikacjami pierwszej firmy, muszą być obsługiwane za pośrednictwem autoryzacji wstępnej. przyczyną tego problemu jest to, że użytkownik jest zalogowany jako nazwa główna usługi. Polecenie powinno zakończyć się pomyślnie, jeśli zostanie wykonane przez użytkownika.

> [!TIP]
> Nie można bezpośrednio utworzyć użytkownika na podstawie Azure Active Directory innego niż Azure Active Directory skojarzony z subskrypcją platformy Azure. Jednak elementy członkowskie innych aktywnych katalogów, które są zaimportowani użytkownicy w skojarzonych Active Directory (nazywanych użytkownikami zewnętrznymi) można dodać do grupy Active Directory w Active Directory dzierżawcy. Utworzenie użytkownika zawartej bazy danych dla tej grupy usługi AD pozwala użytkownikom z Active Directory zewnętrznych uzyskać dostęp do SQL Database.

Aby uzyskać więcej informacji na temat tworzenia użytkowników zawartej bazy danych na podstawie tożsamości Azure Active Directory, zobacz [Create User (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx).

> [!NOTE]
> Usunięcie Azure Active Directory administrator programu Azure SQL Server uniemożliwi innym użytkownikom uwierzytelniania usługi Azure AD nawiązywanie połączenia z serwerem. W razie potrzeby użytkownicy usługi Azure AD, którzy nie będą mogli korzystać, mogą być porzucani ręcznie przez administratora SQL Database.

> [!NOTE]
> Jeśli zostanie wyświetlony komunikat **upłynął limit czasu połączenia**, może być konieczne ustawienie `TransparentNetworkIPResolution` parametru parametrów połączenia na wartość false. Aby uzyskać więcej informacji, zobacz [problem z limitem czasu połączenia w .NET Framework 4.6.1-TransparentNetworkIPResolution](https://blogs.msdn.microsoft.com/dataaccesstechnologies/20../../connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution/).

Podczas tworzenia użytkownika bazy danych użytkownik otrzymuje uprawnienie **Połącz** i może połączyć się z tą bazą danych jako członkiem roli **publicznej** . Początkowo jedynymi uprawnieniami dostępnymi dla użytkownika są uprawnienia przyznane do roli **publicznej** lub wszelkie uprawnienia przyznane do grup usługi Azure AD, których są członkami. Po aprowizacji użytkownika zawartej bazy danych opartej na usłudze Azure AD można udzielić użytkownikom dodatkowych uprawnień w taki sam sposób jak w przypadku każdego innego typu użytkownika. Zazwyczaj przyznaj uprawnienia do ról bazy danych i Dodaj użytkowników do ról. Aby uzyskać więcej informacji, zobacz [podstawowe informacje o uprawnieniach aparatu bazy danych](https://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). Aby uzyskać więcej informacji na temat specjalnych ról SQL Database, zobacz [Zarządzanie bazami danych i nazwami logowania w programie Azure SQL Database](sql-database-manage-logins.md).
Konto użytkownika domeny federacyjnej zaimportowane do domeny zarządzanej jako użytkownik zewnętrzny musi używać tożsamości domeny zarządzanej.

> [!NOTE]
> Użytkownicy usługi Azure AD są oznaczeni w metadanych bazy danych typem E (EXTERNAL_USER), a grupy typem X (EXTERNAL_GROUPS). Aby uzyskać więcej informacji, zobacz [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).

## <a name="connect-to-the-user-database-or-azure-synapse-by-using-ssms-or-ssdt"></a>Łączenie się z bazą danych użytkownika lub usługą Azure Synapse za pomocą programu SSMS lub SSDT  

Aby upewnić się, że administrator usługi Azure AD został prawidłowo skonfigurowany, nawiąż połączenie z bazą danych **Master** przy użyciu konta administratora usługi Azure AD.
Aby udostępnić użytkownikom zawartej bazy danych opartych na usłudze Azure AD (innym niż administrator serwera, który jest właścicielem bazy danych), Połącz się z bazą danych za pomocą tożsamości usługi Azure AD, która ma dostęp do bazy danych.

> [!IMPORTANT]
> Obsługa Azure Active Directorygo uwierzytelniania jest dostępna w [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) i [SQL Server narzędziach](https://msdn.microsoft.com/library/mt204009.aspx) do obsługi danych w programie Visual Studio 2015. Wydanie programu SSMS 2016 sierpnia w sierpniu obejmuje również obsługę uwierzytelniania uniwersalnego Active Directory, co umożliwia administratorom wymaganie Multi-Factor Authentication przy użyciu połączenia telefonicznego, wiadomości tekstowej, kart inteligentnych z numerem PIN lub powiadomień aplikacji mobilnej.

## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>Używanie tożsamości usługi Azure AD do nawiązywania połączenia przy użyciu programu SSMS lub SSDT

Poniższe procedury pokazują, jak nawiązać połączenie z bazą danych SQL za pomocą tożsamości usługi Azure AD przy użyciu narzędzi SQL Server Management Studio lub SQL Server Database. 

### <a name="active-directory-integrated-authentication"></a>Active Directory uwierzytelnianie zintegrowane

Użyj tej metody, jeśli logujesz się do systemu Windows przy użyciu poświadczeń Azure Active Directory z domeny federacyjnej lub domeny zarządzanej, która jest skonfigurowana do bezproblemowego logowania jednokrotnego na potrzeby uwierzytelniania za pośrednictwem funkcji przekazywania i hasła. Aby uzyskać więcej informacji, zobacz [Azure Active Directory bezproblemowe logowanie jednokrotne](../active-directory/hybrid/how-to-connect-sso.md).

1. Uruchom Management Studio lub narzędzia danych, a następnie w oknie dialogowym **łączenie z serwerem** (lub **łączenie z aparatem bazy danych**) w polu **uwierzytelnianie** wybierz pozycję **Azure Active Directory — zintegrowane**. Nie jest potrzebne żadne hasło ani nie można go wprowadzić, ponieważ Twoje istniejące poświadczenia zostaną wyświetlone dla tego połączenia.

    ![Wybieranie uwierzytelniania zintegrowanego usługi AD][11]

2. Wybierz przycisk **Opcje** , a następnie na stronie **Właściwości połączenia** w polu **Połącz z bazą danych** wpisz nazwę bazy danych użytkownika, z którą chcesz nawiązać połączenie. Aby uzyskać więcej informacji, zobacz artykuł [uwierzytelnianie wieloskładnikowe w usłudze AAD](sql-database-ssms-mfa-authentication.md#azure-ad-domain-name-or-tenant-id-parameter) w przypadku różnic między właściwościami połączenia dla programu SSMS 17. x i 18. x. 

    ![Wybierz nazwę bazy danych][13]

### <a name="active-directory-password-authentication"></a>Active Directory uwierzytelnianie hasła

Użyj tej metody podczas nawiązywania połączenia z nazwą główną usługi Azure AD przy użyciu domeny zarządzanej usługi Azure AD. Można go również użyć dla kont federacyjnych bez dostępu do domeny, na przykład podczas pracy zdalnej.

Użyj tej metody, aby uwierzytelnić się w usłudze SQL DB lub MI przy użyciu użytkowników tożsamości tylko w chmurze usługi Azure AD lub tych, którzy korzystają z tożsamości hybrydowych usługi Azure AD. Ta metoda obsługuje użytkowników, którzy chcą korzystać z poświadczeń systemu Windows, ale ich komputer lokalny nie jest przyłączony do domeny (na przykład przy użyciu dostępu zdalnego). W takim przypadku użytkownik systemu Windows może wskazywać swoje konto domeny i hasło, a także może uwierzytelniać się w usłudze SQL DB, MI lub na platformie Azure Synapse.

1. Uruchom Management Studio lub narzędzia danych, a następnie w oknie dialogowym **łączenie z serwerem** (lub **łączenie z aparatem bazy danych**) w polu **uwierzytelnianie** wybierz pozycję **Azure Active Directory-Password**.

2. W polu **Nazwa użytkownika** wpisz nazwę użytkownika Azure Active Directory w formacie **username\@Domain.com**. Nazwy użytkowników muszą być kontami z Azure Active Directory lub konta z domeny zarządzanej lub federacyjnej z Azure Active Directory.

3. W polu **hasło** wpisz hasło użytkownika dla konta Azure Active Directory lub konta domeny zarządzane/federacyjne.

    ![Wybieranie opcji uwierzytelnianie hasła usługi AD][12]

4. Wybierz przycisk **Opcje** , a następnie na stronie **Właściwości połączenia** w polu **Połącz z bazą danych** wpisz nazwę bazy danych użytkownika, z którą chcesz nawiązać połączenie. (Zobacz grafika w poprzedniej opcji).

### <a name="active-directory-interactive-authentication"></a>Active Directory uwierzytelnianie interaktywne

Ta metoda służy do interaktywnego uwierzytelniania z uwierzytelnianiem wieloskładnikowym lub bez niego, z hasłem wymaganym interaktywnie. Ta metoda może służyć do uwierzytelniania w usłudze SQL DB, MI i Azure Synapse dla użytkowników tożsamości tylko w chmurze usługi Azure AD lub tych, którzy korzystają z tożsamości hybrydowych usługi Azure AD.

Aby uzyskać więcej informacji, zobacz [Korzystanie z uwierzytelniania wieloskładnikowego w usłudze AAD przy użyciu Azure SQL Database i usługi Azure Synapse Analytics (Obsługa programu SSMS)](sql-database-ssms-mfa-authentication.md).

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Używanie tożsamości usługi Azure AD do nawiązywania połączenia z aplikacji klienckiej

Poniższe procedury pokazują, jak nawiązać połączenie z bazą danych SQL za pomocą tożsamości usługi Azure AD z aplikacji klienckiej.

### <a name="active-directory-integrated-authentication"></a>Active Directory uwierzytelnianie zintegrowane

Aby można było korzystać ze zintegrowanego uwierzytelniania systemu Windows, Active Directory domeny musi być federacyjny z Azure Active Directory lub być domeną zarządzaną, która jest skonfigurowana do bezproblemowego logowania jednokrotnego do uwierzytelniania za pomocą przekazywania lub skrótu hasła. Aby uzyskać więcej informacji, zobacz [Azure Active Directory bezproblemowe logowanie jednokrotne](../active-directory/hybrid/how-to-connect-sso.md).

> [!NOTE]
> [MSAL.NET (Microsoft. Identity. Client)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap) na potrzeby zintegrowanego uwierzytelniania systemu Windows nie jest obsługiwane w celu zapewnienia bezproblemowego logowania jednokrotnego na potrzeby uwierzytelniania w postaci przekazywania i skrótu hasła.

Aplikacja kliencka (lub usługa) łącząca się z bazą danych musi być uruchomiona na komputerze przyłączonym do domeny w ramach poświadczeń domeny użytkownika.

Aby nawiązać połączenie z bazą danych przy użyciu uwierzytelniania zintegrowanego i tożsamości usługi Azure AD, słowo kluczowe uwierzytelniania w parametrach połączenia bazy `Active Directory Integrated`danych musi być ustawione na wartość. Poniższy przykładowy kod w języku C# używa ADO .NET.

```csharp
string ConnectionString = @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Słowo kluczowe `Integrated Security=True` parametrów połączenia nie jest obsługiwane w przypadku nawiązywania połączenia z Azure SQL Database. Podczas tworzenia połączenia ODBC należy usunąć spacje i ustawić uwierzytelnianie na "ActiveDirectoryIntegrated".

### <a name="active-directory-password-authentication"></a>Active Directory uwierzytelnianie hasła

Aby nawiązać połączenie z bazą danych przy użyciu kont użytkowników tożsamości tylko w chmurze usługi Azure AD lub tych, którzy korzystają z tożsamości hybrydowych usługi Azure AD, `Active Directory Password`słowo kluczowe uwierzytelniania musi być ustawione na wartość. Parametry połączenia muszą zawierać słowa kluczowe ID/UID i Password/PWD oraz wartości. Poniższy przykładowy kod w języku C# używa ADO .NET.

```csharp
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Dowiedz się więcej na temat metod uwierzytelniania usługi Azure AD przy użyciu przykładów kodu demonstracyjnego dostępnych w [demonstracji usługi GitHub w usłudze Azure AD](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth).

## <a name="azure-ad-token"></a>Token usługi Azure AD

Ta metoda uwierzytelniania umożliwia usługom warstwy środkowej uzyskanie [tokenów sieci Web JSON (JWT)](../active-directory/develop/id-tokens.md) w celu nawiązania połączenia z usługą Azure SQL Database lub Azure Synapse przez uzyskanie tokenu z Azure Active Directory (AAD). Ta metoda zapewnia różne scenariusze aplikacji, w tym tożsamości usług, nazwy główne usług i aplikacje korzystające z uwierzytelniania opartego na certyfikatach. Należy wykonać cztery podstawowe kroki, aby użyć uwierzytelniania przy użyciu tokenu usługi Azure AD:

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

Aby uzyskać więcej informacji, zobacz Blog dotyczący [zabezpieczeń SQL Server](https://blogs.msdn.microsoft.com/sqlsecurity/20../../token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/). Informacje o dodawaniu certyfikatu znajdują się [w temacie Wprowadzenie do uwierzytelniania opartego na certyfikatach w Azure Active Directory](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).

### <a name="sqlcmd"></a>sqlcmd

Poniższe instrukcje łączą się przy użyciu wersji 13,1 narzędzia sqlcmd, która jest dostępna w [Centrum pobierania](https://www.microsoft.com/download/details.aspx?id=53591).

> [!NOTE]
> `sqlcmd``-G` polecenie nie działa z tożsamościami systemu i wymaga logowania do podmiotu zabezpieczeń użytkownika.

```cmd
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="troubleshooting-azure-ad-authentication"></a>Rozwiązywanie problemów z uwierzytelnianiem w usłudze Azure AD

Wskazówki dotyczące rozwiązywania problemów z uwierzytelnianiem za pomocą usługi Azure AD można znaleźć w następującym blogu:<https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991>

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem nazw logowania, użytkowników, ról bazy danych i uprawnień w SQL Database, zobacz [logowania, użytkowników, role bazy danych i konta użytkowników](sql-database-manage-logins.md).
- Aby uzyskać więcej informacji na temat podmiotów zabezpieczeń bazy danych, zobacz [Principals](https://msdn.microsoft.com/library/ms181127.aspx) (Podmioty zabezpieczeń).
- Aby uzyskać więcej informacji na temat ról bazy danych, zobacz [Database roles](https://msdn.microsoft.com/library/ms189121.aspx) (Role bazy danych).
- Aby uzyskać więcej informacji na temat reguł zapory w usłudze SQL Database, zobacz [Omówienie reguł zapory usługi SQL Database](sql-database-firewall-configure.md).

<!--Image references-->
[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/active-directory-integrated.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth2.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db2.png
