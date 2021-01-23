---
title: Wyświetlanie przypisań ról niestandardowych w portalu Azure Active Directory | Microsoft Docs
description: Teraz można wyświetlać członków roli administrator Azure Active Directory w centrum administracyjnym Azure Active Directory i zarządzać nimi.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c13167706c2794253108b04eaa2dcc46e0e6c09a
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98740690"
---
# <a name="view-custom-role-assignments-using-azure-active-directory"></a>Wyświetlanie przypisań ról niestandardowych przy użyciu Azure Active Directory

W tym artykule opisano sposób wyświetlania ról niestandardowych przypisanych w Azure Active Directory (Azure AD). W Azure Active Directory (Azure AD) role można przypisywać w zakresie całej organizacji lub z zakresem pojedynczej aplikacji.

- Przypisania ról w zakresie całej organizacji są dodawane do i mogą być widoczne na liście przypisań roli pojedynczej aplikacji.
- Przypisania ról w ramach pojedynczego zakresu aplikacji nie są dodawane do i nie mogą być widoczne na liście przypisań zakresów w całej organizacji.

## <a name="view-role-assignments-in-the-azure-portal"></a>Wyświetlanie przypisań ról w Azure Portal

Ta procedura opisuje Wyświetlanie przypisań roli z zakresem całej organizacji.

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy użyciu uprawnień administrator ról uprzywilejowanych lub Administrator globalny w organizacji usługi Azure AD.
1. Wybierz pozycję **Azure Active Directory**, wybierz pozycję **role i Administratorzy**, a następnie wybierz rolę, aby ją otworzyć i wyświetlić jej właściwości.
1. Wybierz pozycję **przypisania** , aby wyświetlić przypisania dla tej roli.

    ![Wyświetlanie przypisań ról i uprawnień po otwarciu roli z listy](./media/view-assignments/role-assignments.png)

## <a name="view-role-assignments-using-azure-ad-powershell"></a>Wyświetlanie przypisań ról przy użyciu programu Azure AD PowerShell

W tej sekcji opisano Wyświetlanie przypisań roli z zakresem całej organizacji. W tym artykule jest wykorzystywany moduł [Azure Active Directory PowerShell w wersji 2](/powershell/module/azuread/#directory_roles) . Aby wyświetlić przypisania zakresów pojedynczych aplikacji za pomocą programu PowerShell, można użyć poleceń cmdlet w [przypisywać role niestandardowe przy użyciu programu PowerShell](custom-assign-powershell.md).

### <a name="prepare-powershell"></a>Przygotowywanie programu PowerShell

Najpierw należy [pobrać moduł programu PowerShell usługi Azure AD Preview](https://www.powershellgallery.com/packages/AzureAD/).

Aby zainstalować moduł Azure AD PowerShell, użyj następujących poleceń:

``` PowerShell
install-module azureadpreview
import-module azureadpreview
```

Aby sprawdzić, czy moduł jest gotowy do użycia, użyj następującego polecenia:

``` PowerShell
get-module azuread
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}
```

### <a name="view-the-assignments-of-a-role"></a>Wyświetlanie przypisań roli

Przykład wyświetlania przypisań roli.

``` PowerShell
# Fetch list of all directory roles with object ID
Get-AzureADDirectoryRole

# Fetch a specific directory role by ID
$role = Get-AzureADDirectoryRole -ObjectId "5b3fe201-fa8b-4144-b6f1-875829ff7543"

# Fetch role membership for a role
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADUser
```

## <a name="view-role-assignments-using-microsoft-graph-api"></a>Wyświetlanie przypisań ról przy użyciu interfejsu API Microsoft Graph

W tej sekcji opisano Wyświetlanie przypisań roli z zakresem całej organizacji.  Aby wyświetlić przypisania zakresów pojedynczych aplikacji przy użyciu interfejs API programu Graph, można użyć operacji w [przypisaniu ról niestandardowych z interfejs API programu Graph](custom-assign-graph.md).

Żądanie HTTP, aby uzyskać przypisanie roli dla danej definicji roli.

GET

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments&$filter=roleDefinitionId eq ‘<object-id-or-template-id-of-role-definition>’
```

Reakcja

``` HTTP
HTTP/1.1 200 OK
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":["/"]
}
```

## <a name="view-assignments-of-single-application-scope"></a>Wyświetlanie przypisań zakresu pojedynczej aplikacji

W tej sekcji opisano Wyświetlanie przypisań roli z zakresem pojedynczej aplikacji. Ta funkcja jest obecnie w publicznej wersji zapoznawczej.

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy użyciu uprawnień administrator ról uprzywilejowanych lub Administrator globalny w organizacji usługi Azure AD.
1. Wybierz pozycję **rejestracje aplikacji**, a następnie wybierz pozycję Rejestracja aplikacji, aby wyświetlić jej właściwości. Może być konieczne wybranie opcji **wszystkie aplikacje** , aby wyświetlić pełną listę rejestracji aplikacji w organizacji usługi Azure AD.

    ![Tworzenie lub edytowanie rejestracji aplikacji na stronie Rejestracje aplikacji](./media/view-assignments/app-reg-all-apps.png)

1. W obszarze Rejestracja aplikacji wybierz pozycję **role i Administratorzy**, a następnie wybierz rolę, aby wyświetlić jej właściwości.

    ![Wyświetlanie przypisań ról rejestracji aplikacji ze strony Rejestracje aplikacji](./media/view-assignments/app-reg-assignments.png)

1. Wybierz pozycję **przypisania** , aby wyświetlić przypisania dla tej roli. Otwarcie widoku przypisania w ramach rejestracji aplikacji powoduje wyświetlenie przypisań objętych zakresem tego zasobu usługi Azure AD.

    ![Wyświetlanie przypisań ról rejestracji aplikacji z właściwości rejestracji aplikacji](./media/view-assignments/app-reg-assignments-2.png)

## <a name="next-steps"></a>Następne kroki

* Podziel się z nami na [forum ról administracyjnych usługi Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Aby uzyskać więcej informacji o rolach i przypisaniu roli administratora, zobacz [Przypisywanie ról administratorów](permissions-reference.md).
* Domyślne uprawnienia użytkownika można znaleźć w [porównaniu z domyślnymi uprawnieniami gościa i użytkownika](../fundamentals/users-default-permissions.md).
