---
title: Tworzenie ról niestandardowych w kontroli dostępu opartej na rolach usługi Azure AD | Microsoft Docs
description: Utwórz i przypisz niestandardowe role usługi Azure AD z zakresem zasobów dla zasobów Azure Active Directory.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 01/05/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e0e1543f18c18c7fdf97c39f35ba38ded658392
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103007821"
---
# <a name="create-and-assign-a-custom-role-in-azure-active-directory"></a>Tworzenie i przypisywanie roli niestandardowej w Azure Active Directory

W tym artykule opisano sposób tworzenia nowych ról niestandardowych w usłudze Azure Active Directory (Azure AD). Podstawowe informacje na temat ról niestandardowych można znaleźć w temacie [role niestandardowe — Omówienie](custom-overview.md). Rolę można przypisać zarówno w zakresie poziomu katalogu, jak i w zakresie zasobów rejestracji aplikacji.

Role niestandardowe można tworzyć na karcie [role i Administratorzy](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) na stronie Przegląd usługi Azure AD.

## <a name="create-a-role-in-the-azure-portal"></a>Tworzenie roli w Azure Portal

### <a name="create-a-new-custom-role-to-grant-access-to-manage-app-registrations"></a>Utwórz nową rolę niestandardową, aby udzielić dostępu do zarządzania rejestracjami aplikacji

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy użyciu uprawnień administrator ról uprzywilejowanych lub Administrator globalny w organizacji usługi Azure AD.
1. Wybierz pozycję **Azure Active Directory**  >  **role i Administratorzy**  >  **Nowa rola niestandardowa**.

   ![Tworzenie lub edytowanie ról na stronie role i Administratorzy](./media/custom-create/new-custom-role.png)

1. Na karcie **podstawowe** Podaj nazwę i opis roli, a następnie kliknij przycisk **dalej**.

   ![Podaj nazwę i opis roli niestandardowej na karcie podstawowe](./media/custom-create/basics-tab.png)

1. Na karcie **uprawnienia** wybierz uprawnienia wymagane do zarządzania podstawowymi właściwościami i właściwościami poświadczeń rejestracji aplikacji. Aby uzyskać szczegółowy opis każdego z tych uprawnień, zobacz [podtypy rejestracji aplikacji i uprawnienia w Azure Active Directory](custom-available-permissions.md).
   1. Najpierw wprowadź wartość "poświadczenia" na pasku wyszukiwania i wybierz `microsoft.directory/applications/credentials/update` uprawnienie.

      ![Wybierz uprawnienia roli niestandardowej na karcie uprawnienia](./media/custom-create/permissions-tab.png)

   1. Następnie wprowadź wartość "podstawowa" na pasku wyszukiwania, wybierz `microsoft.directory/applications/basic/update` uprawnienie, a następnie kliknij przycisk **dalej**.
1. Na karcie **Recenzja + tworzenie** Przejrzyj uprawnienia i wybierz pozycję **Utwórz**.

Rola niestandardowa zostanie wyświetlona na liście dostępnych ról do przypisania.

## <a name="create-a-role-using-powershell"></a>Tworzenie roli przy użyciu programu PowerShell

### <a name="prepare-powershell"></a>Przygotowywanie programu PowerShell

Najpierw należy [pobrać moduł programu PowerShell usługi Azure AD Preview](https://www.powershellgallery.com/packages/AzureADPreview).

Aby zainstalować moduł Azure AD PowerShell, użyj następujących poleceń:

``` PowerShell
Install-Module -Name AzureADPreview 
Import-Module -Name AzureADPreview 
```

Aby sprawdzić, czy moduł jest gotowy do użycia, użyj następującego polecenia:

``` PowerShell
Get-Module -Name AzureADPreview 

  ModuleType Version      Name                         ExportedCommands 
  ---------- ---------    ----                         ---------------- 
  Binary     2.0.0.115    AzureADPreview               {Add-AzureADAdministrati...} 
```

### <a name="connect-to-azure"></a>Nawiązywanie połączenia z usługą Azure

Aby nawiązać połączenie z Azure Active Directory, użyj następującego polecenia:

``` PowerShell
Connect-AzureAD
```

### <a name="create-the-custom-role"></a>Utwórz rolę niestandardową

Utwórz nową rolę przy użyciu następującego skryptu programu PowerShell:

``` PowerShell
# Basic role information
$displayName = "Application Support Administrator"
$description = "Can manage basic aspects of application registrations."
$templateId = (New-Guid).Guid
 
# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/basic/update",
    "microsoft.directory/applications/credentials/update"
)
$rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}
 
# Create new custom admin role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>Przypisywanie roli niestandardowej przy użyciu programu Azure AD PowerShell

Przypisz rolę przy użyciu poniższego skryptu programu PowerShell:

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

## <a name="create-a-role-with-graph-api"></a>Tworzenie roli przy użyciu interfejs API programu Graph

1. Utwórz definicję roli.

    Żądanie HTTP, aby utworzyć niestandardową definicję roli.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
    ```

    Treść

    ``` HTTP
    {
       "description": "Can manage basic aspects of application registrations.",
       "displayName": "Application Support Administrator",
       "isEnabled": true,
       "templateId": "<GUID>",
       "rolePermissions": [
           {
               "allowedResourceActions": [
                   "microsoft.directory/applications/basic/update",
                   "microsoft.directory/applications/credentials/update"
               ]
           }
       ]
    }
    ```

    > [!Note]
    > `"templateId": "GUID"`Jest to opcjonalny parametr, który jest wysyłany w treści w zależności od wymagania. Jeśli istnieje potrzeba utworzenia wielu różnych ról niestandardowych ze wspólnymi parametrami, najlepszym rozwiązaniem jest utworzenie szablonu i zdefiniowanie `templateId` wartości. Wartość można wygenerować `templateId` wcześniej przy użyciu polecenia cmdlet programu PowerShell `(New-Guid).Guid` . 

1. Utwórz przypisanie roli.

    Żądanie HTTP, aby utworzyć niestandardową definicję roli.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
    ```

    Treść

    ``` HTTP
   {
       "principalId":"<GUID OF USER>",
       "roleDefinitionId":"<GUID OF ROLE DEFINITION>",
       "resourceScope":"/<GUID OF APPLICATION REGISTRATION>"
   }
    ```

## <a name="assign-a-custom-role-scoped-to-a-resource"></a>Przypisywanie roli niestandardowej do zasobu

Podobnie jak wbudowane role, role niestandardowe są domyślnie przypisywane do zakresu całego całej organizacji, aby przyznać uprawnienia dostępu do wszystkich rejestracji aplikacji w organizacji. Jednak w przeciwieństwie do wbudowanych ról role niestandardowe można także przypisać do zakresu pojedynczego zasobu usługi Azure AD. Dzięki temu użytkownik może przyznać użytkownikowi uprawnienia do aktualizowania poświadczeń i podstawowych właściwości pojedynczej aplikacji bez konieczności tworzenia drugiej roli niestandardowej.

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy użyciu uprawnień dewelopera aplikacji w organizacji usługi Azure AD.
1. Wybierz pozycję **Rejestracje aplikacji**.
1. Wybierz rejestrację aplikacji, do której chcesz udzielić dostępu do zarządzania. Może być konieczne wybranie opcji **wszystkie aplikacje** , aby wyświetlić pełną listę rejestracji aplikacji w organizacji usługi Azure AD.

    ![Wybierz rejestrację aplikacji jako zakres zasobów dla przypisania roli](./media/custom-create/appreg-all-apps.png)

1. W obszarze Rejestracja aplikacji wybierz pozycję **role i Administratorzy**. Jeśli jeszcze tego nie zrobiono, instrukcje znajdują się w [poprzedniej procedurze](#create-a-new-custom-role-to-grant-access-to-manage-app-registrations).

1. Wybierz rolę, aby otworzyć stronę **przypisania** .
1. Wybierz pozycję **Dodaj przypisanie** , aby dodać użytkownika. Użytkownikowi zostaną przydzielone wszystkie uprawnienia tylko do wybranej rejestracji aplikacji.

## <a name="next-steps"></a>Następne kroki

- Podziel się z nami na [forum ról administracyjnych usługi Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- Aby uzyskać więcej informacji o rolach i przypisaniu roli administratora, zobacz [Przypisywanie ról administratorów](permissions-reference.md).
- Domyślne uprawnienia użytkownika można znaleźć w [porównaniu z domyślnymi uprawnieniami gościa i użytkownika](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2froles%2fcontext%2fugr-context).