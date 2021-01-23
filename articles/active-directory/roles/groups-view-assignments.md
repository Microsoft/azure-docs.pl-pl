---
title: Wyświetlanie ról przypisanych do grupy w Azure Active Directory | Microsoft Docs
description: Dowiedz się, w jaki sposób role przypisane do grupy mogą być wyświetlane przy użyciu Centrum administracyjnego usługi Azure AD. Wyświetlanie grup i przypisanych ról jest domyślnymi uprawnieniami użytkownika.
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
ms.openlocfilehash: 1a1939be42126606fdae261e60c890c71374c894
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98741829"
---
# <a name="view-roles-assigned-to-a-group-in-azure-active-directory"></a>Wyświetlanie ról przypisanych do grupy w Azure Active Directory

W tej sekcji opisano, w jaki sposób role przypisane do grupy mogą być wyświetlane przy użyciu Centrum administracyjnego usługi Azure AD. Wyświetlanie grup i przypisanych ról jest domyślnymi uprawnieniami użytkownika.

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) przy użyciu poświadczeń innych niż administracyjne lub administratora.

1. Wybierz interesującą Cię grupę.

1. Wybierz **przypisane role**. Teraz można zobaczyć wszystkie role usługi Azure AD przypisane do tej grupy.

   ![Wyświetl wszystkie role przypisane do wybranej grupy](./media/groups-view-assignments/view-assignments.png)

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

- [Zarządzanie przypisaniami ról za pomocą grup w chmurze](groups-concept.md)
- [Rozwiązywanie problemów z rolami przypisanymi do grup w chmurze](groups-faq-troubleshooting.md)
