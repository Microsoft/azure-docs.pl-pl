---
title: Wyświetlanie ról przypisanych do grupy w Azure Active Directory | Microsoft Docs
description: Wyświetl podgląd niestandardowych ról usługi Azure AD na potrzeby delegowania zarządzania tożsamościami. Zarządzanie rolami platformy Azure w Azure Portal, PowerShell lub interfejs API programu Graph.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/27/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b7613fb70299a70e4389b97c2647a26cb7c3374
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87476090"
---
# <a name="view-roles-assigned-to-a-group-in-azure-active-directory"></a>Wyświetlanie ról przypisanych do grupy w Azure Active Directory

W tej sekcji opisano, w jaki sposób role przypisane do grupy mogą być wyświetlane przy użyciu Centrum administracyjnego usługi Azure AD. Wyświetlanie grup i przypisanych ról jest domyślnymi uprawnieniami użytkownika.

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) przy użyciu poświadczeń innych niż administracyjne lub administratora.

1. Wybierz interesującą Cię grupę.

1. Wybierz **przypisane role**. Teraz można zobaczyć wszystkie role usługi Azure AD przypisane do tej grupy.

   ![Wyświetl wszystkie role przypisane do wybranej grupy](./media/roles-groups-view-assignments/view-assignments.png)

## <a name="using-powershell"></a>Korzystanie z programu PowerShell

### <a name="get-object-id-of-the-group"></a>Pobierz identyfikator obiektu grupy

```powershell
Get-AzureADMSGroup -SearchString “Contoso_Helpdesk_Administrators”
```

### <a name="view-role-assignment-to-a-group"></a>Wyświetl przypisanie roli do grupy

```powershell
Get-AzureADMSRoleAssignment -Filter "principalId eq '<object id of group>" 
```

## <a name="using-microsoft-graph-api"></a>Korzystanie z interfejsu API Microsoft Graph

### <a name="get-object-id-of-the-group"></a>Pobierz identyfikator obiektu grupy

```powershell
GET https://graph.microsoft.com/beta/groups?$filter displayName eq ‘Contoso_Helpdesk_Administrator’ 
```

### <a name="get-role-assignments-to-a-group"></a>Pobieranie przypisań ról do grupy

```powershell
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments?$filter=principalId eq
```

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie przypisaniami ról przy użyciu grup chmur](roles-groups-concept.md)
- [Rozwiązywanie problemów z rolami przypisanymi do grup chmury](roles-groups-faq-troubleshooting.md)
