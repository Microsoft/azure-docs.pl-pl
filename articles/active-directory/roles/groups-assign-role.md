---
title: Przypisywanie roli do grupy w chmurze w Azure Active Directory | Microsoft Docs
description: Przypisz rolę usługi Azure AD do grupy przypisanej do roli w Azure Portal, PowerShell lub interfejs API programu Graph.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 816009abb688525cd7663311c79300a6d12cf146
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98742951"
---
# <a name="assign-a-role-to-a-cloud-group-in-azure-active-directory"></a>Przypisywanie roli do grupy w chmurze w Azure Active Directory

W tej sekcji opisano, w jaki sposób administrator IT może przypisać rolę Azure Active Directory (Azure AD) do grupy usługi Azure AD.

## <a name="using-azure-ad-admin-center"></a>Korzystanie z centrum administracyjnego usługi Azure AD

Przypisywanie grupy do roli usługi Azure AD jest podobne do przypisywania użytkowników i jednostek usługi, z wyjątkiem tego, że mogą być używane tylko grupy, które są przypisywane do ról. W Azure Portal są wyświetlane tylko grupy, które są przypisane do roli.

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) przy użyciu uprawnień administrator ról uprzywilejowanych lub Administrator globalny w organizacji usługi Azure AD.

1. Wybierz pozycję **Azure Active Directory**  >  **role i Administratorzy**, a następnie wybierz rolę, którą chcesz przypisać.

1. Na stronie ***Nazwa roli** _ wybierz pozycję > _ * Dodaj przypisanie * *.

   ![Dodaj nowe przypisanie roli](./media/groups-assign-role/add-assignment.png)

1. Wybierz grupę. Wyświetlane są tylko te grupy, które można przypisać do ról usługi Azure AD.

    [![Dla nowego przypisania roli są wyświetlane tylko te grupy, które są przypisywane.](./media/groups-assign-role/eligible-groups.png "Dla nowego przypisania roli są wyświetlane tylko te grupy, które są przypisywane.")](./media/groups-assign-role/eligible-groups.png#lightbox)

1. Wybierz pozycję **Dodaj**.

Aby uzyskać więcej informacji na temat przypisywania uprawnień roli, zobacz [Przypisywanie ról administratora i niebędących administratorami do użytkowników](../fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="using-powershell"></a>Korzystanie z programu PowerShell

### <a name="create-a-group-that-can-be-assigned-to-role"></a>Tworzenie grupy, która może być przypisana do roli

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true 
```

### <a name="get-the-role-definition-for-the-role-you-want-to-assign"></a>Pobierz definicję roli dla roli, którą chcesz przypisać

```powershell
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Helpdesk Administrator'" 
```

### <a name="create-a-role-assignment"></a>Utwórz przypisanie roli

```powershell
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $group.Id 
```

## <a name="using-microsoft-graph-api"></a>Korzystanie z interfejsu API Microsoft Graph

### <a name="create-a-group-that-can-be-assigned-azure-ad-role"></a>Utwórz grupę, do której można przypisać rolę usługi Azure AD

```
POST https://graph.microsoft.com/beta/groups
{
"description": "This group is assigned to Helpdesk Administrator built-in role of Azure AD.",
"displayName": "Contoso_Helpdesk_Administrators",
"groupTypes": [
"Unified"
],
"mailEnabled": true,
"securityEnabled": true
"mailNickname": "contosohelpdeskadministrators",
"isAssignableToRole": true,
}
```

### <a name="get-the-role-definition"></a>Pobierz definicję roli

```
GET https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions?$filter = displayName eq ‘Helpdesk Administrator’
```

### <a name="create-the-role-assignment"></a>Utwórz przypisanie roli

```
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
{
"principalId":"<Object Id of Group>",
"roleDefinitionId":"<ID of role definition>",
"directoryScopeId":"/"
}
```
## <a name="next-steps"></a>Następne kroki

- [Zarządzanie przypisaniami ról za pomocą grup w chmurze](groups-concept.md)
- [Rozwiązywanie problemów z rolami przypisanymi do grup w chmurze](groups-faq-troubleshooting.md)
