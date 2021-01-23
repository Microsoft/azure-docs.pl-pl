---
title: Uprawnienia roli niestandardowej do przypisań dostępu do aplikacji dla przedsiębiorstw — Azure Active Directory | Microsoft Docs
description: Utwórz i przypisz niestandardowe role usługi Azure AD na potrzeby dostępu do aplikacji dla przedsiębiorstw w Azure Active Directory
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3cb65503ffab610f9545acb313f7284ffb11ed1
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98741149"
---
# <a name="assign-custom-roles-to-manage-enterprise-apps-in-azure-active-directory"></a>Przypisywanie ról niestandardowych w celu zarządzania aplikacjami dla przedsiębiorstw w programie Azure Active Directory

W tym artykule wyjaśniono, jak utworzyć rolę niestandardową z uprawnieniami do zarządzania przypisaniami aplikacji dla przedsiębiorstw dla użytkowników i grup w Azure Active Directory (Azure AD). W przypadku elementów przypisania ról i znaczenia warunków, takich jak podtyp, uprawnienie i zestaw właściwości, zobacz [Omówienie ról niestandardowych](custom-overview.md).

## <a name="enterprise-app-role-permissions"></a>Uprawnienia roli aplikacji dla przedsiębiorstw

W tym artykule omówiono dwie uprawnienia do aplikacji dla przedsiębiorstw. Wszystkie przykłady używają uprawnienia do aktualizacji.

* Aby odczytać przydziały użytkowników i grup w zakresie, udziel `microsoft.directory/servicePrincipals/appRoleAssignedTo/read` uprawnienia
* Aby zarządzać przypisaniami użytkowników i grup w zakresie, udziel `microsoft.directory/servicePrincipals/appRoleAssignedTo/update` uprawnienia

Przyznanie uprawnień do aktualizacji powoduje, że przydziały mogą zarządzać przypisaniami użytkowników i grup do aplikacji dla przedsiębiorstw. Zakres przypisań użytkowników i/lub grup można przyznać dla jednej aplikacji lub udzielonej wszystkim aplikacjom. W przypadku przyznania na poziomie całej organizacji przydziały mogą zarządzać przypisaniami dla wszystkich aplikacji. Jeśli na poziomie aplikacji, osoba przydzielona może zarządzać przypisaniami tylko do określonej aplikacji.

Przyznanie uprawnienia do aktualizacji odbywa się w dwóch krokach:

1. Tworzenie roli niestandardowej z uprawnieniem `microsoft.directory/servicePrincipals/appRoleAssignedTo/update`
1. Przyznaj użytkownikom lub grupom uprawnienia do zarządzania przypisaniami użytkowników i grup w aplikacjach dla przedsiębiorstw. Jest to możliwe, gdy można ustawić zakres na poziomie całej organizacji lub do pojedynczej aplikacji.

## <a name="use-the-azure-ad-admin-center"></a>Korzystanie z centrum administracyjnego usługi Azure AD

### <a name="create-a-new-custom-role"></a>Utwórz nową rolę niestandardową

>[!NOTE]
> Role niestandardowe są tworzone i zarządzane na poziomie całej organizacji i są dostępne tylko na stronie Przegląd w organizacji.

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy użyciu uprawnień administrator ról uprzywilejowanych lub Administrator globalny w organizacji.
1. Wybierz pozycję **Azure Active Directory**, wybierz pozycję **role i Administratorzy**, a następnie wybierz pozycję **Nowa rola niestandardowa**.

    ![Dodawanie nowej roli niestandardowej z listy role w usłudze Azure AD](./media/custom-enterprise-apps/new-custom-role.png)

1. Na karcie **podstawowe** podaj wartość w polu "Zarządzaj przypisaniami użytkowników i grup" w polu Nazwa roli i "Udziel uprawnień do zarządzania przypisaniami użytkowników i grup", a następnie wybierz przycisk **dalej**.

    ![Podaj nazwę i opis roli niestandardowej](./media/custom-enterprise-apps/role-name-and-description.png)

1. Na karcie **uprawnienia** wprowadź wartość "Microsoft. Directory/serviceprincipals/appRoleAssignedTo/Update" w polu wyszukiwania, a następnie zaznacz pola wyboru obok żądanych uprawnień, a następnie wybierz przycisk **dalej**.

    ![Dodawanie uprawnień do roli niestandardowej](./media/custom-enterprise-apps/role-custom-permissions.png)

1. Na karcie **Recenzja + tworzenie** Przejrzyj uprawnienia i wybierz pozycję **Utwórz**.

    ![Teraz można utworzyć rolę niestandardową](./media/custom-enterprise-apps/role-custom-create.png)

### <a name="assign-the-role-to-a-user-using-the-azure-ad-portal"></a>Przypisywanie roli do użytkownika przy użyciu portalu usługi Azure AD

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy użyciu uprawnień roli administrator ról uprzywilejowanych.
1. Wybierz pozycję **Azure Active Directory** a następnie wybierz pozycję **role i Administratorzy**.
1. Wybierz **uprawnienia Udziel uprawnień do zarządzania przypisaniami użytkowników i grup** .

    ![Otwórz przystawkę role i Administratorzy i Wyszukaj rolę niestandardową](./media/custom-enterprise-apps/select-custom-role.png)

1. Wybierz pozycję **Dodaj przypisanie**, wybierz żądanego użytkownika, a następnie kliknij pozycję **Wybierz** , aby dodać przypisanie roli do użytkownika.

    ![Dodaj przypisanie dla roli niestandardowej do użytkownika](./media/custom-enterprise-apps/assign-user-to-role.png)

#### <a name="assignment-tips"></a>Wskazówki dotyczące przypisywania

* Aby udzielić uprawnień użytkownikom i dostępom do grup dla wszystkich aplikacji dla przedsiębiorstw, należy zacząć od **ról i administratorów** w całej organizacji na stronie **Przegląd** usługi Azure AD w organizacji.
* Aby udzielić uprawnień do przydziałów do zarządzania użytkownikami i dostępem do grupy dla określonej aplikacji dla przedsiębiorstw, przejdź do tej aplikacji w usłudze Azure AD i otwórz ją na liście **role i Administratorzy** dla tej aplikacji. Wybierz nową rolę niestandardową i Ukończ przypisanie użytkownika lub grupy. Osoby przydzielone mogą zarządzać dostępem użytkowników i grup tylko dla określonej aplikacji.
* Aby przetestować niestandardowe przypisanie roli, zaloguj się jako osoba przydzielona i Otwórz stronę **Użytkownicy i grupy** aplikacji, aby upewnić się, że opcja **Dodaj użytkownika** jest włączona.

    ![Weryfikowanie uprawnień użytkownika](./media/custom-enterprise-apps/verify-user-permissions.png)

## <a name="use-azure-ad-powershell"></a>Korzystanie z programu Azure AD PowerShell

Aby uzyskać więcej informacji, zobacz [Tworzenie i przypisywanie roli niestandardowej](custom-create.md) i [Przypisywanie ról niestandardowych z zakresem zasobów przy użyciu programu PowerShell](custom-assign-powershell.md).

Najpierw zainstaluj moduł Azure AD PowerShell z [Galeria programu PowerShell](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Następnie zaimportuj moduł programu Azure AD PowerShell w wersji zapoznawczej przy użyciu następującego polecenia:

```powershell
PowerShell
import-module azureadpreview
```

Aby sprawdzić, czy moduł jest gotowy do użycia, Dopasuj wersję zwróconą przez następujące polecenie do wymienionego poniżej:

```powershell
PowerShell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADAdministrati...}
```

### <a name="create-a-custom-role"></a>Tworzenie roli niestandardowej

Utwórz nową rolę przy użyciu następującego skryptu programu PowerShell:

```PowerShell
# Basic role information
$description = "Manage user and group assignments"
$displayName = "Can manage user and group assignments for Applications"
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =@( "microsoft.directory/servicePrincipals/appRoleAssignedTo/update")
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role"></a>Przypisywanie roli niestandardowej

Przypisz rolę przy użyciu tego skryptu programu PowerShell.

```powershell
PowerShell
# Basic role information

$description = "Manage user and group assignments"
$displayName = "Can manage user and group assignments for Applications"
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/servicePrincipals/appRoleAssignedTo/update"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom role
$customRole = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

## <a name="use-the-microsoft-graph-api"></a>Korzystanie z interfejsu API programu Microsoft Graph

Utwórz rolę niestandardową przy użyciu podanego przykładu w interfejsie API Microsoft Graph. Aby uzyskać więcej informacji, zobacz [Tworzenie i przypisywanie roli niestandardowej](custom-create.md) i [przypisywanie niestandardowych ról administratora przy użyciu interfejsu API Microsoft Graph](custom-assign-graph.md).

Żądanie HTTP, aby utworzyć rolę niestandardową.

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

### <a name="assign-the-custom-role-using-microsoft-graph-api"></a>Przypisywanie roli niestandardowej przy użyciu interfejsu API Microsoft Graph

Przypisanie roli łączy Identyfikator podmiotu zabezpieczeń (który może być podmiotem użytkownika lub usługi), identyfikator definicji roli i zakres zasobów usługi Azure AD. Aby uzyskać więcej informacji na temat elementów przypisania roli, zobacz [Omówienie ról niestandardowych](custom-overview.md)

Żądanie HTTP do przypisania roli niestandardowej.

```HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments

{
    "principalId":"<PROVIDE OBJECTID OF USER TO ASSIGN HERE>",
    "roleDefinitionId":"<PROVIDE OBJECTID OF ROLE DEFINITION HERE>",
    "resourceScopes":["/"]
}
```

## <a name="next-steps"></a>Następne kroki

* [Poznaj dostępne uprawnienia roli niestandardowej dla aplikacji dla przedsiębiorstw](custom-enterprise-app-permissions.md)
