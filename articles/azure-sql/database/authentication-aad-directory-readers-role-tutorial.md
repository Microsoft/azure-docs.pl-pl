---
title: Przypisywanie roli czytelnicy katalogów do grupy usługi Azure AD i zarządzanie przypisaniami ról
description: Ten artykuł przeprowadzi Cię przez włączenie roli czytniki katalogów za pomocą grup usługi Azure AD do zarządzania przypisaniami ról usługi Azure AD za pomocą usługi Azure SQL Database, wystąpienia zarządzanego Azure SQL i usługi Azure Synapse Analytics
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: tutorial
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/14/2020
ms.openlocfilehash: bc809cf02b827b7498890cb7d929c44bd360ab53
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2021
ms.locfileid: "99094712"
---
# <a name="tutorial-assign-directory-readers-role-to-an-azure-ad-group-and-manage-role-assignments"></a>Samouczek: Przypisywanie roli czytelnicy katalogów do grupy usługi Azure AD i zarządzanie przypisaniami ról

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

> [!NOTE]
> Przypisanie roli **czytelnicy katalogów** do grupy w tym artykule jest w **publicznej wersji zapoznawczej**. 

Ten artykuł przeprowadzi Cię przez proces tworzenia grupy w Azure Active Directory (Azure AD) i przypisywania grupy roli [**czytelnicy katalogów**](../../active-directory/roles/permissions-reference.md#directory-readers) . Uprawnienia odczyty katalogów umożliwiają właścicielom grupy Dodawanie dodatkowych członków do grupy, takich jak [zarządzana tożsamość](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) [Azure SQL Database](sql-database-paas-overview.md), [wystąpienia zarządzanego usługi Azure SQL](../managed-instance/sql-managed-instance-paas-overview.md)i [usługi Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Pozwala to pominąć potrzebę uprawnienia [administratora globalnego](../../active-directory/roles/permissions-reference.md#global-administrator) lub [administratora roli uprzywilejowanej](../../active-directory/roles/permissions-reference.md#privileged-role-administrator) do przypisywania roli czytelnicy katalogów bezpośrednio dla każdej tożsamości serwera logicznego usługi Azure SQL w dzierżawie.

W tym samouczku użyto funkcji wprowadzonej w temacie [Używanie grup w chmurze do zarządzania przypisaniami ról w programie Azure Active Directory (wersja zapoznawcza)](../../active-directory/roles/groups-concept.md). 

Aby uzyskać więcej informacji na temat korzyści z przypisywania roli czytelnicy Directory do grupy usługi Azure AD dla usługi Azure SQL, zobacz temat [rola czytelnicy Directory w Azure Active Directory dla usługi Azure SQL](authentication-aad-directory-readers-role.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Wystąpienie usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Konfigurowanie uwierzytelniania usługi Azure AD i zarządzanie nim za pomocą usługi Azure SQL](authentication-aad-configure.md).
- SQL Database, wystąpienie zarządzane SQL lub usługa Azure Synapse.

## <a name="directory-readers-role-assignment-using-the-azure-portal"></a>Przypisanie roli czytniki katalogów za pomocą Azure Portal

### <a name="create-a-new-group-and-assign-owners-and-role"></a>Utwórz nową grupę i przypisz właścicieli i rolę

1. Użytkownik z uprawnieniami [administratora globalnego](../../active-directory/roles/permissions-reference.md#global-administrator) lub [administratorem roli uprzywilejowanej](../../active-directory/roles/permissions-reference.md#privileged-role-administrator) jest wymagany dla tej początkowej konfiguracji.
1. Zalogować się do [Azure Portal](https://portal.azure.com)przez użytkownika uprzywilejowanego.
1. Przejdź do zasobu **Azure Active Directory** . W obszarze **zarządzane** przejdź do pozycji **grupy**. Wybierz pozycję **Nowa grupa** , aby utworzyć nową grupę.
1. Wybierz pozycję **zabezpieczenia** jako typ grupy i wypełnij pozostałe pola. Upewnij się, że ustawienie **role usługi Azure AD można przypisać do grupy (wersja zapoznawcza)** jest przełączane na **wartość tak**. Następnie przypisz rolę **czytelnicy katalogów** usługi Azure AD do grupy.
1. Przypisz użytkowników usługi Azure AD jako właścicieli do grupy, która została utworzona. Właściciel grupy może być zwykłym użytkownikiem usługi AD bez przypisanej roli administracyjnej usługi Azure AD. Właściciel powinien być użytkownikiem, który zarządza SQL Database, wystąpieniem zarządzanym SQL lub usługą Azure Synapse.

   :::image type="content" source="media/authentication-aad-directory-readers-role/new-group.png" alt-text="AAD — Nowa grupa":::

1. Wybierz pozycję **Utwórz**

### <a name="checking-the-group-that-was-created"></a>Sprawdzanie utworzonej grupy

> [!NOTE]
> Upewnij się, że **Typ grupy** to **zabezpieczenia**. Grupy *Microsoft 365* nie są obsługiwane w przypadku usługi Azure SQL.

Aby sprawdzić utworzoną grupę i zarządzać nią, Wróć do okienka **grupy** w Azure Portal i wyszukaj nazwę grupy. Po wybraniu grupy można dodać dodatkowych właścicieli i członków z menu **właściciele** i **Członkowie** w obszarze **Zarządzanie** ustawieniami. Można również przejrzeć **przypisane role** dla grupy.

:::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-group-created.png" alt-text="Zrzut ekranu okienka grupy z linkami otwierającymi menu Ustawienia dla elementów członkowskich, właściciele i przypisane role (wersja zapoznawcza) wyróżnione.":::

### <a name="add-azure-sql-managed-identity-to-the-group"></a>Dodaj tożsamość zarządzaną Azure SQL do grupy

> [!NOTE]
> W tym przykładzie używamy wystąpienia zarządzanego przez usługę SQL, ale do uzyskania tych samych wyników można zastosować podobne kroki dla SQL Database lub Azure Synapse.

W przypadku kolejnych kroków Administrator globalny lub administrator ról uprzywilejowanych nie jest już wymagany.

1. Zaloguj się do Azure Portal jako użytkownik zarządzający wystąpieniem zarządzanym SQL i jest właścicielem grupy utworzonej wcześniej.

1. Znajdź nazwę zasobu **wystąpienia zarządzanego SQL** w Azure Portal.

   :::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-managed-instance.png" alt-text="Zrzut ekranu przedstawiający ekran wystąpienia zarządzane SQL z nazwą wystąpienia SQL ssomitest i podświetloną nazwą podsieci ManagedInstance.":::

   Podczas tworzenia wystąpienia zarządzanego SQL utworzono tożsamość platformy Azure dla danego wystąpienia. Utworzona tożsamość ma taką samą nazwę jak prefiks nazwy wystąpienia zarządzanego SQL. Jednostkę usługi dla tożsamości wystąpienia zarządzanego SQL, która została utworzona jako aplikacja usługi Azure AD, można znaleźć, wykonując następujące czynności:

    - Przejdź do zasobu **Azure Active Directory** . W obszarze Ustawienia **zarządzania** wybierz pozycję **aplikacje dla przedsiębiorstw**. **Identyfikator obiektu** jest tożsamością wystąpienia.
    
    :::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-managed-instance-service-principal.png" alt-text="Zrzut ekranu strony aplikacje dla przedsiębiorstw dla zasobu Azure Active Directory z IDENTYFIKATORem obiektu wyróżnionego wystąpienia zarządzanego SQL.":::

1. Przejdź do zasobu **Azure Active Directory** . W obszarze **zarządzane** przejdź do pozycji **grupy**. Wybierz utworzoną grupę. W obszarze Ustawienia **zarządzane** grupy wybierz pozycję **Członkowie**. Wybierz pozycję **Dodaj członków** i Dodaj jednostkę usługi wystąpienia zarządzanego SQL jako element członkowski grupy, wyszukując znalezioną powyżej nazwę.

   :::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-add-managed-instance-service-principal.png" alt-text="Zrzut ekranu strony członkowie dla zasobu Azure Active Directory z opcjami wyróżnionymi w celu dodania wystąpienia zarządzanego SQL jako nowego elementu członkowskiego.":::

> [!NOTE]
> Propagowanie uprawnień głównych usługi za pomocą systemu Azure i Zezwalanie na dostęp do usługi Azure AD interfejs API programu Graph może potrwać kilka minut. Może być konieczne odczekanie kilku minut przed udostępnieniem usługi Azure AD Admin dla wystąpienia zarządzanego SQL.

### <a name="remarks"></a>Uwagi

W przypadku SQL Database i usługi Azure Synapse tożsamość serwera można utworzyć podczas tworzenia serwera logicznego usługi Azure SQL lub po utworzeniu serwera. Aby uzyskać więcej informacji na temat tworzenia lub ustawiania tożsamości serwera w SQL Database lub Azure Synapse, zobacz [Włączanie nazw głównych usług w celu tworzenia użytkowników usługi Azure AD](authentication-aad-service-principal.md#enable-service-principals-to-create-azure-ad-users).

W przypadku wystąpienia zarządzanego SQL rola **czytelnicy Directory** musi być przypisana do tożsamości wystąpienia zarządzanego, aby można było [skonfigurować administratora usługi Azure AD dla wystąpienia zarządzanego](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).

Przypisanie roli **czytniki katalogów** do tożsamości serwera nie jest wymagane dla SQL Database lub Azure Synapse podczas konfigurowania administratora usługi Azure AD dla serwera logicznego. Aby umożliwić tworzenie obiektów usługi Azure AD w SQL Database lub Azure Synapse w imieniu aplikacji usługi Azure AD, wymagana jest rola **czytelnicy katalogów** . Jeśli rola nie jest przypisana do tożsamości serwera logicznego SQL, tworzenie użytkowników usługi Azure AD w usłudze Azure SQL zakończy się niepowodzeniem. Aby uzyskać więcej informacji, zobacz [Azure Active Directory jednostki usługi przy użyciu usługi Azure SQL](authentication-aad-service-principal.md).

## <a name="directory-readers-role-assignment-using-powershell"></a>Przypisanie roli czytniki katalogów przy użyciu programu PowerShell

> [!IMPORTANT]
> Administrator [globalny](../../active-directory/roles/permissions-reference.md#global-administrator) lub [administrator ról uprzywilejowanych](../../active-directory/roles/permissions-reference.md#privileged-role-administrator) będzie musiał wykonać te kroki. Oprócz programu PowerShell usługa Azure AD oferuje Microsoft Graph interfejs API do [tworzenia grupy z możliwością przypisywania ról w usłudze Azure AD](../../active-directory/roles/groups-create-eligible.md#using-microsoft-graph-api).

1. Pobierz moduł programu PowerShell usługi Azure AD w wersji zapoznawczej przy użyciu następujących poleceń. Może być konieczne uruchomienie programu PowerShell jako administrator.

    ```powershell
    Install-Module azureadpreview
    Import-Module azureadpreview
    #To verify that the module is ready to use, use the following command:
    Get-Module azureadpreview
    ```

1. Nawiąż połączenie z dzierżawą usługi Azure AD.

    ```powershell
    Connect-AzureAD
    ```

1. Utwórz grupę zabezpieczeń, aby przypisać rolę **czytelnicy Directory** .

    - `DirectoryReaderGroup`, `Directory Reader Group` i `DirRead` można zmienić zgodnie z preferencjami.

    ```powershell
    $group = New-AzureADMSGroup -DisplayName "DirectoryReaderGroup" -Description "Directory Reader Group" -MailEnabled $False -SecurityEnabled $true -MailNickName "DirRead" -IsAssignableToRole $true
    $group
    ```

1. Przypisz rolę **czytelnicy Directory** do grupy.

    ```powershell
    # Displays the Directory Readers role information
    $roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Directory Readers'" 
    $roleDefinition
    ```

    ```powershell
    # Assigns the Directory Readers role to the group
    $roleAssignment = New-AzureADMSRoleAssignment -ResourceScope '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $group.Id
    $roleAssignment
    ```

1. Przypisz właścicieli do grupy.

    - Zamień na `<username>` użytkownika, który ma być własnością tej grupy. Kilku właścicieli można dodać przez powtórzenie tych kroków.

    ```powershell
    $RefObjectID = Get-AzureADUser -ObjectId "<username>"
    $RefObjectID
    ```

    ```powershell
    $GrOwner = Add-AzureADGroupOwner -ObjectId $group.ID -RefObjectId $RefObjectID.ObjectID
    ```

    Sprawdź właścicieli grupy przy użyciu następującego polecenia:

    ```powershell
    Get-AzureADGroupOwner -ObjectId $group.ID
    ```

    Możesz również zweryfikować właścicieli grupy w [Azure Portal](https://portal.azure.com). Wykonaj kroki opisane w temacie [Sprawdzanie, czy utworzono grupę](#checking-the-group-that-was-created).

### <a name="assigning-the-service-principal-as-a-member-of-the-group"></a>Przypisywanie jednostki usługi jako członka grupy

W przypadku kolejnych kroków Administrator globalny lub administrator ról uprzywilejowanych nie jest już wymagany.

1. Przy użyciu właściciela grupy, który również zarządza zasobem Azure SQL, uruchom następujące polecenie, aby nawiązać połączenie z usługą Azure AD.

    ```powershell
    Connect-AzureAD
    ```

1. Przypisz jednostkę usługi jako członka grupy, która została utworzona.

    - Zastąp ciąg `<ServerName>` nazwą serwera logicznego usługi Azure SQL lub nazwą wystąpienia zarządzanego. Aby uzyskać więcej informacji, zobacz sekcję [Dodawanie tożsamości usługi Azure SQL do grupy](#add-azure-sql-managed-identity-to-the-group)

    ```powershell
    # Returns the service principal of your Azure SQL resource
    $miIdentity = Get-AzureADServicePrincipal -SearchString "<ServerName>"
    $miIdentity
    ```

    ```powershell
    # Adds the service principal to the group as a member
    Add-AzureADGroupMember -ObjectId $group.ID -RefObjectId $miIdentity.ObjectId 
    ```

    Poniższe polecenie zwróci identyfikator obiektu jednostki usługi wskazujący, że został dodany do grupy:

    ```powershell
    Add-AzureADGroupMember -ObjectId $group.ID -RefObjectId $miIdentity.ObjectId
    ```

## <a name="next-steps"></a>Następne kroki

- [Rola czytelnicy Directory w Azure Active Directory dla usługi Azure SQL](authentication-aad-directory-readers-role.md)
- [Samouczek: tworzenie użytkowników usługi Azure AD przy użyciu aplikacji usługi Azure AD](authentication-aad-service-principal-tutorial.md)
- [Konfigurowanie uwierzytelniania usługi Azure AD i zarządzanie nim za pomocą usługi Azure SQL](authentication-aad-configure.md)