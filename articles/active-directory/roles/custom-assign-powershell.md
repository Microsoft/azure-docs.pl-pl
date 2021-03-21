---
title: Przypisywanie ról niestandardowych przy użyciu programu Azure AD PowerShell — Azure AD | Microsoft Docs
description: Zarządzanie członkami roli niestandardowej administratora usługi Azure AD przy użyciu programu Azure AD PowerShell.
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
ms.openlocfilehash: 9f0fb81a4daa57b473e8b2b4b937426eafbf903d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103014540"
---
# <a name="assign-custom-roles-with-resource-scope-using-powershell-in-azure-active-directory"></a>Przypisywanie ról niestandardowych z zakresem zasobów przy użyciu programu PowerShell w programie Azure Active Directory

W tym artykule opisano sposób tworzenia przypisania roli w zakresie całej organizacji w Azure Active Directory (Azure AD). Przypisywanie roli w zakresie szerokiej organizacji daje dostęp w całej organizacji usługi Azure AD. Aby utworzyć przypisanie roli z zakresem pojedynczego zasobu usługi Azure AD, zobacz [jak utworzyć rolę niestandardową i przypisać ją do zakresu zasobów](custom-create.md). W tym artykule jest wykorzystywany moduł [Azure Active Directory PowerShell w wersji 2](/powershell/module/azuread/#directory_roles) .

Aby uzyskać więcej informacji na temat ról administratorów usługi Azure AD, zobacz [Przypisywanie ról administratorów w Azure Active Directory](permissions-reference.md).

## <a name="required-permissions"></a>Wymagane uprawnienia

Połącz się z organizacją usługi Azure AD przy użyciu konta administratora globalnego do przypisywania lub usuwania ról.

## <a name="prepare-powershell"></a>Przygotowywanie programu PowerShell

Zainstaluj moduł Azure AD PowerShell z [Galeria programu PowerShell](https://www.powershellgallery.com/packages/AzureADPreview). Następnie zaimportuj moduł programu Azure AD PowerShell w wersji zapoznawczej przy użyciu następującego polecenia:

``` PowerShell
Import-Module -Name AzureADPreview
```

Aby sprawdzić, czy moduł jest gotowy do użycia, Dopasuj wersję zwróconą przez następujące polecenie do wymienionego poniżej:

``` PowerShell
Get-Module -Name AzureADPreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    AzureADPreview               {Add-AzureADMSAdministrati...}
```

Teraz możesz zacząć korzystać z poleceń cmdlet w module. Aby zapoznać się z pełnymi opisami poleceń cmdlet w module usługi Azure AD, zobacz dokumentację referencyjną online [modułu usługi Azure AD w wersji zapoznawczej](https://www.powershellgallery.com/packages/AzureADPreview).

## <a name="assign-a-directory-role-to-a-user-or-service-principal-with-resource-scope"></a>Przypisywanie roli katalogu do użytkownika lub nazwy głównej usługi z zakresem zasobów

1. Załaduj moduł programu Azure AD PowerShell (wersja zapoznawcza).
1. Zaloguj się, wykonując polecenie `Connect-AzureAD` .
1. Utwórz nową rolę przy użyciu poniższego skryptu programu PowerShell.

``` PowerShell
## Assign a role to a user or service principal with resource scope
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

Aby przypisać rolę do jednostki usługi zamiast użytkownika, należy użyć polecenia cmdlet [Get-AzureADMSServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) .

## <a name="role-definitions"></a>Definicje ról

Obiekty definicji ról zawierają definicję wbudowanej lub niestandardowej roli wraz z uprawnieniami przyznanymi przez to przypisanie roli. Ten zasób zawiera definicje ról niestandardowych i wbudowane role katalogów (które są wyświetlane w definicji równoważnej). Obecnie organizacja usługi Azure AD może mieć zdefiniowane maksymalnie 30 unikatowych definicji ról niestandardowych.

### <a name="create-a-role-definition"></a>Tworzenie definicji roli

``` PowerShell
# Basic information
$description = "Can manage credentials of application registrations"
$displayName = "Application Registration Credential Administrator"
$templateId = (New-Guid).Guid

# Set of actions to include
$rolePermissions = @{
    "allowedResourceActions" = @(
        "microsoft.directory/applications/standard/read",
        "microsoft.directory/applications/credentials/update"
    )
}

# Create new custom directory role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="read-and-list-role-definitions"></a>Odczytuj i wyświetlaj definicje ról

``` PowerShell
# Get all role definitions
Get-AzureADMSRoleDefinitions

# Get single role definition by ID
Get-AzureADMSRoleDefinition -Id 86593cfc-114b-4a15-9954-97c3494ef49b

# Get single role definition by templateId
Get-AzureADMSRoleDefinition -Filter "templateId eq 'c4e39bd9-1100-46d3-8c65-fb160da0071f'"
```

### <a name="update-a-role-definition"></a>Aktualizowanie definicji roli

``` PowerShell
# Update role definition
# This works for any writable property on role definition. You can replace display name with other
# valid properties.
Set-AzureADMSRoleDefinition -Id c4e39bd9-1100-46d3-8c65-fb160da0071f -DisplayName "Updated DisplayName"
```

### <a name="delete-a-role-definition"></a>Usuwanie definicji roli

``` PowerShell
# Delete role definition
Remove-AzureADMSRoleDefinitions -Id c4e39bd9-1100-46d3-8c65-fb160da0071f
```

## <a name="role-assignments"></a>Przypisania ról

Przypisania ról zawierają informacje łączące dany podmiot zabezpieczeń (nazwę główną użytkownika lub usługi aplikacji) z definicją roli. W razie potrzeby można dodać zakres pojedynczego zasobu usługi Azure AD dla przypisanych uprawnień.  Ograniczanie zakresu przypisania roli jest obsługiwane w przypadku ról wbudowanych i niestandardowych.

### <a name="create-a-role-assignment"></a>Utwórz przypisanie roli

``` PowerShell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

### <a name="read-and-list-role-assignments"></a>Odczytaj i Wyświetl listę przypisań ról

``` PowerShell
# Get role assignments for a given principal
Get-AzureADMSRoleAssignment -Filter "principalId eq '27c8ca78-ab1c-40ae-bd1b-eaeebd6f68ac'"

# Get role assignments for a given role definition 
Get-AzureADMSRoleAssignment -Filter "roleDefinitionId eq '355aed8a-864b-4e2b-b225-ea95482e7570'"
```

### <a name="delete-a-role-assignment"></a>Usuwanie przypisania roli

``` PowerShell
# Delete role assignment
Remove-AzureADMSRoleAssignment -Id 'qiho4WOb9UKKgng_LbPV7tvKaKRCD61PkJeKMh7Y458-1'
```

## <a name="next-steps"></a>Następne kroki

- Podziel się z nami na [forum ról administracyjnych usługi Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)
- Aby uzyskać więcej informacji o rolach i przypisaniach ról administratora usługi Azure AD, zobacz [Przypisywanie ról administratorów](permissions-reference.md)
- Aby uzyskać domyślne uprawnienia użytkownika, zobacz [porównanie domyślnych uprawnień gościa i użytkownika](../fundamentals/users-default-permissions.md)
