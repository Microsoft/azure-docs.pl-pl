---
title: Tworzenie ról niestandardowych do zarządzania aplikacjami dla przedsiębiorstw w Azure Active Directory
description: Tworzenie i przypisywanie niestandardowych ról usługi Azure AD w celu uzyskiwania dostępu do aplikacji dla przedsiębiorstw w Azure Active Directory
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 04/14/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7c04afe76ced0abf40abf8e30362005fb269172
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534723"
---
# <a name="create-custom-roles-to-manage-enterprise-apps-in-azure-active-directory"></a>Tworzenie ról niestandardowych do zarządzania aplikacjami dla przedsiębiorstw w Azure Active Directory

W tym artykule wyjaśniono, jak utworzyć rolę niestandardową z uprawnieniami do zarządzania przypisaniami aplikacji przedsiębiorstwa dla użytkowników i grup w usłudze Azure Active Directory (Azure AD). Aby uzyskać informacje na temat elementów przypisań ról i znaczenia terminów, takich jak podtyp, uprawnienia i zestaw właściwości, zobacz omówienie [ról niestandardowych.](custom-overview.md)

## <a name="enterprise-app-role-permissions"></a>Uprawnienia roli aplikacji dla przedsiębiorstw

W tym artykule omówiono dwa uprawnienia aplikacji przedsiębiorstwa. We wszystkich przykładach używa się uprawnienia do aktualizacji.

* Aby odczytać przypisania użytkowników i grup w zakresie, przyznaj `microsoft.directory/servicePrincipals/appRoleAssignedTo/read` uprawnienie
* Aby zarządzać przypisaniami użytkowników i grup w zakresie, przyznaj `microsoft.directory/servicePrincipals/appRoleAssignedTo/update` uprawnienie

Przyznanie uprawnień do aktualizacji powoduje, że przypisani mogą zarządzać przypisaniami użytkowników i grup do aplikacji przedsiębiorstwa. Zakres przypisań użytkowników i/lub grup można przyznać dla jednej aplikacji lub dla wszystkich aplikacji. Jeśli zostanie udzielony na poziomie całej organizacji, może zarządzać przypisaniami dla wszystkich aplikacji. Jeśli zostaną wykonane na poziomie aplikacji, przypisany może zarządzać przypisaniami tylko dla określonej aplikacji.

Udzielanie uprawnień do aktualizacji odbywa się w dwóch krokach:

1. Tworzenie roli niestandardowej z uprawnieniami `microsoft.directory/servicePrincipals/appRoleAssignedTo/update`
1. Przyznaj użytkownikom lub grupom uprawnienia do zarządzania przypisaniami użytkowników i grup do aplikacji przedsiębiorstwa. W takiej sytuacji można ustawić zakres na poziomie całej organizacji lub na pojedynczą aplikację.

## <a name="use-the-azure-ad-admin-center"></a>Korzystanie z centrum administracyjnego usługi Azure AD

### <a name="create-a-new-custom-role"></a>Tworzenie nowej roli niestandardowej

>[!NOTE]
> Role niestandardowe są tworzone i zarządzane na poziomie całej organizacji i są dostępne tylko na stronie Przegląd organizacji.

1. Zaloguj się do centrum [administracyjnego usługi Azure AD przy](https://aad.portal.azure.com) użyciu uprawnień administratora ról uprzywilejowanych lub administratora globalnego w organizacji.
1. Wybierz **Azure Active Directory** pozycję Role **i administratorzy,** a następnie wybierz **pozycję Nowa rola niestandardowa.**

    ![Dodawanie nowej roli niestandardowej z listy ról w usłudze Azure AD](./media/custom-enterprise-apps/new-custom-role.png)

1. Na karcie **Podstawowe** podaj tekst "Zarządzanie przypisaniami użytkowników i grup" dla nazwy roli i "Uporządkuj uprawnienia do zarządzania przypisaniami użytkowników i grup" jako opis roli, a następnie wybierz pozycję **Dalej.**

    ![Podaj nazwę i opis roli niestandardowej](./media/custom-enterprise-apps/role-name-and-description.png)

1. Na karcie **Uprawnienia** wprowadź wartość "microsoft.directory/servicePrincipals/appRoleAssignedTo/update" w polu wyszukiwania, a następnie zaznacz pola wyboru obok odpowiednich uprawnień, a następnie wybierz przycisk **Dalej.**

    ![Dodawanie uprawnień do roli niestandardowej](./media/custom-enterprise-apps/role-custom-permissions.png)

1. Na karcie **Przeglądanie + tworzenie** przejrzyj uprawnienia i wybierz pozycję **Utwórz.**

    ![Teraz możesz utworzyć rolę niestandardową](./media/custom-enterprise-apps/role-custom-create.png)

### <a name="assign-the-role-to-a-user-using-the-azure-ad-portal"></a>Przypisywanie roli do użytkownika przy użyciu portalu usługi Azure AD

1. Zaloguj się do centrum [administracyjnego usługi Azure AD przy](https://aad.portal.azure.com) użyciu uprawnień roli administratora ról uprzywilejowanych.
1. Wybierz **Azure Active Directory** a następnie wybierz pozycję **Role i administratorzy.**
1. Wybierz rolę **Uporządkuj uprawnienia, aby zarządzać przypisaniami użytkowników i** grup.

    ![Otwórz okno Role i administratorzy i wyszukaj rolę niestandardową](./media/custom-enterprise-apps/select-custom-role.png)

1. Wybierz **pozycję Dodaj przypisanie,** wybierz żądanego użytkownika, a następnie kliknij pozycję **Wybierz,** aby dodać przypisanie roli do użytkownika.

    ![Dodawanie przypisania roli niestandardowej do użytkownika](./media/custom-enterprise-apps/assign-user-to-role.png)

#### <a name="assignment-tips"></a>Wskazówki dotyczące przypisywania

* Aby udzielić przypisaniom uprawnień do zarządzania użytkownikami i dostępem grup dla  wszystkich aplikacji przedsiębiorstwa w całej organizacji, zacznij od listy Role i administratorzy w całej organizacji na stronie Przeglądu usługi Azure **AD** dla organizacji.
* Aby udzielić przypisaniom uprawnień do zarządzania użytkownikami i dostępem grup dla określonej aplikacji przedsiębiorstwa, przejdź do tej aplikacji w usłudze Azure AD i otwórz aplikację na liście **Role** i administratorzy dla tej aplikacji. Wybierz nową rolę niestandardową i ukończ przypisanie użytkownika lub grupy. Przypisani mogą zarządzać użytkownikami i dostępem do grup tylko dla określonej aplikacji.
* Aby przetestować przypisanie roli niestandardowej, zaloguj się jako przypisz i otwórz stronę  Użytkownicy i **grupy** aplikacji, aby sprawdzić, czy opcja Dodaj użytkownika jest włączona.

    ![Weryfikowanie uprawnień użytkownika](./media/custom-enterprise-apps/verify-user-permissions.png)

## <a name="use-azure-ad-powershell"></a>Korzystanie z programu PowerShell usługi Azure AD

Aby uzyskać więcej informacji, zobacz Create and assign a custom role (Tworzenie i przypisywanie [roli niestandardowej) oraz](custom-create.md) Assign custom roles with resource scope using PowerShell (Przypisywanie ról niestandardowych [z zakresem zasobów przy użyciu programu PowerShell).](custom-assign-powershell.md)

Najpierw zainstaluj moduł Azure AD PowerShell z [witryny Galeria programu PowerShell](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Następnie zaimportuj moduł azure AD PowerShell w wersji zapoznawczej przy użyciu następującego polecenia:

```powershell
Import-Module -Name AzureADPreview
```

Aby sprawdzić, czy moduł jest gotowy do użycia, dopasuj wersję zwróconą przez następujące polecenie do wersji wymienionej tutaj:

```powershell
Get-Module -Name AzureADPreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    AzureADPreview               {Add-AzureADAdministrati...}
```

### <a name="create-a-custom-role"></a>Tworzenie roli niestandardowej

Utwórz nową rolę przy użyciu następującego skryptu programu PowerShell:

```PowerShell
# Basic role information
$description = "Manage user and group assignments"
$displayName = "Can manage user and group assignments for Applications"
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction = @("microsoft.directory/servicePrincipals/appRoleAssignedTo/update")
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role"></a>Przypisywanie roli niestandardowej

Przypisz rolę przy użyciu tego skryptu programu PowerShell.

```powershell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'chandra@example.com'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Manage user and group assignments'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'My Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

## <a name="use-the-microsoft-graph-api"></a>Korzystanie z interfejsu API programu Microsoft Graph

Utwórz rolę niestandardową przy użyciu podanego przykładu w interfejsie API Microsoft Graph API. Aby uzyskać więcej informacji, zobacz Create and assign a custom role (Tworzenie i przypisywanie [roli niestandardowej)](custom-create.md) oraz Assign custom admin roles using the Microsoft Graph API (Przypisywanie ról administratora niestandardowego przy [użyciu Microsoft Graph API).](custom-assign-graph.md)

Żądanie HTTP utworzenia roli niestandardowej.

```HTTP
POST
https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitionsIsEnabled $true
{
    "description":"Can manage user and group assignments for Applications.",
    "displayName":" Manage user and group assignments",
    "isEnabled":true,
    "rolePermissions":
    [
        {
            "resourceActions":
            {
                "allowedResourceActions":
                [
                    "microsoft.directory/servicePrincipals/appRoleAssignedTo/update"
                ]
            },
            "condition":null
        }
    ],
    "templateId":"<PROVIDE NEW GUID HERE>",
    "version":"1"
}
```

### <a name="assign-the-custom-role-using-microsoft-graph-api"></a>Przypisywanie roli niestandardowej przy użyciu interfejsu API Microsoft Graph API

Przypisanie roli łączy identyfikator podmiotu zabezpieczeń (który może być użytkownikiem lub jednostką usługi), identyfikator definicji roli i zakres zasobów usługi Azure AD. Aby uzyskać więcej informacji na temat elementów przypisania roli, zobacz [omówienie ról niestandardowych](custom-overview.md)

Żądanie HTTP przypisania roli niestandardowej.

```HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments

{
    "principalId":"<PROVIDE OBJECTID OF USER TO ASSIGN HERE>",
    "roleDefinitionId":"<PROVIDE OBJECTID OF ROLE DEFINITION HERE>",
    "resourceScopes":["/"]
}
```

## <a name="next-steps"></a>Następne kroki

* [Eksplorowanie dostępnych uprawnień roli niestandardowej dla aplikacji przedsiębiorstwa](custom-enterprise-app-permissions.md)
