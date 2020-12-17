---
title: Przypisywanie tożsamości zarządzanej do roli aplikacji przy użyciu programu PowerShell — Azure AD
description: Instrukcje krok po kroku dotyczące przypisywania zarządzanej tożsamości dostępu do roli innej aplikacji przy użyciu programu PowerShell.
services: active-directory
documentationcenter: ''
author: johndowns
manager: ''
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2020
ms.author: jodowns
ms.collection: M365-identity-device-management
ms.openlocfilehash: 409ba7a954830bb2370ce83989b9e8b08b742fe7
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97631180"
---
# <a name="assign-a-managed-identity-access-to-an-application-role-using-powershell"></a>Przypisywanie zarządzanej tożsamości dostępu do roli aplikacji przy użyciu programu PowerShell

Zarządzane tożsamości dla zasobów platformy Azure zapewniają usługi platformy Azure z tożsamością w Azure Active Directory. Działają one bez konieczności podania poświadczeń w kodzie. Usługi platformy Azure używają tej tożsamości do uwierzytelniania w usługach, które obsługują uwierzytelnianie w usłudze Azure AD. Role aplikacji zapewniają formę kontroli dostępu opartej na rolach i umożliwiają usłudze implementowanie reguł autoryzacji.

W tym artykule dowiesz się, jak przypisać zarządzaną tożsamość do roli aplikacji udostępnionej przez inną aplikację przy użyciu programu Azure AD PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz tożsamości zarządzanych dla zasobów platformy Azure, zapoznaj się z [sekcją przegląd](overview.md). **Pamiętaj, aby zapoznać się z [różnicą między przypisaną przez system i tożsamością zarządzaną przez użytkownika](overview.md#managed-identity-types)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Do uruchomienia przykładowych skryptów są dostępne dwie opcje:
    - Użyj [Azure Cloud Shell](../../cloud-shell/overview.md), którą można otworzyć za pomocą przycisku **Wypróbuj** w prawym górnym rogu bloków kodu.
    - Uruchom skrypty lokalnie, instalując najnowszą wersję programu [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2).

## <a name="assign-a-managed-identity-access-to-another-applications-app-role"></a>Przypisywanie zarządzanej tożsamości dostępu do roli aplikacji innej aplikacji

1. Włącz zarządzaną tożsamość w ramach zasobu platformy Azure, na przykład [maszynę wirtualną platformy Azure](qs-configure-powershell-windows-vm.md).

1. Znajdź identyfikator obiektu nazwy głównej usługi tożsamości zarządzanej.

   W **przypadku tożsamości zarządzanej przypisanej do systemu** można znaleźć identyfikator obiektu na Azure Portal na stronie **tożsamości** zasobu. Możesz również użyć następującego skryptu programu PowerShell, aby znaleźć identyfikator obiektu. Wymagany jest identyfikator zasobu zasobu utworzonego w kroku 1, który jest dostępny w Azure Portal na stronie **Właściwości** zasobu.

    ```powershell
    $resourceIdWithManagedIdentity = '/subscriptions/{my subscription ID}/resourceGroups/{my resource group name}/providers/Microsoft.Compute/virtualMachines/{my virtual machine name}'
    (Get-AzResource -ResourceId $resourceIdWithManagedIdentity).Identity.PrincipalId
    ```

    W **przypadku tożsamości zarządzanej przypisanej przez użytkownika** można znaleźć identyfikator obiektu tożsamości zarządzanej na Azure Portal na stronie **Przegląd** zasobu. Możesz również użyć następującego skryptu programu PowerShell, aby znaleźć identyfikator obiektu. Wymagany jest identyfikator zasobu tożsamości zarządzanej przypisanej przez użytkownika.

    ```powershell
    $userManagedIdentityResourceId = '/subscriptions/{my subscription ID}/resourceGroups/{my resource group name}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{my managed identity name}'
    (Get-AzResource -ResourceId $userManagedIdentityResourceId).Properties.PrincipalId
    ```

1. Utwórz nową rejestrację aplikacji, która będzie reprezentować usługę, do której zostanie wysłane żądanie przez zarządzaną tożsamość. Jeśli interfejs API lub usługa, która uwidacznia rolę aplikacji przyznającej tożsamość zarządzaną, ma już nazwę główną usługi w dzierżawie usługi Azure AD, Pomiń ten krok. Jeśli na przykład chcesz udzielić zarządzanej tożsamości dostępu do interfejsu API Microsoft Graph, możesz pominąć ten krok.

1. Znajdź identyfikator obiektu nazwy głównej usługi aplikacji usługi. Można to znaleźć przy użyciu Azure Portal. Przejdź do Azure Active Directory i Otwórz stronę **aplikacje dla przedsiębiorstw** , a następnie znajdź aplikację i Wyszukaj **Identyfikator obiektu**. Identyfikator obiektu jednostki usługi można również znaleźć według jego nazwy wyświetlanej za pomocą następującego skryptu programu PowerShell:

    ```powershell
    $serverServicePrincipalObjectId = (Get-AzureADServicePrincipal -Filter "DisplayName eq '$applicationName'").ObjectId
    ```

    > [!NOTE]
    > Nazwy wyświetlane dla aplikacji nie są unikatowe, dlatego należy sprawdzić poprawność jednostki usługi aplikacji.

1. Dodaj [rolę aplikacji](../develop/howto-add-app-roles-in-azure-ad-apps.md) do aplikacji utworzonej w kroku 3. Rolę można utworzyć przy użyciu Azure Portal lub Microsoft Graph. Można na przykład dodać rolę aplikacji w następujący sposób:

    ```json
    {
        "allowedMemberTypes": [
            "Application"
        ],
        "displayName": "Read data from MyApi",
        "id": "0566419e-bb95-4d9d-a4f8-ed9a0f147fa6",
        "isEnabled": true,
        "description": "Allow the application to read data as itself.",
        "value": "MyApi.Read.All"
    }
    ```

1. Przypisz rolę aplikacji do zarządzanej tożsamości. Aby przypisać rolę aplikacji, potrzebne są następujące informacje:
    * `managedIdentityObjectId`: identyfikator obiektu jednostki usługi tożsamości zarządzanej, który został znaleziony w kroku 2.
    * `serverServicePrincipalObjectId`: identyfikator obiektu nazwy głównej usługi aplikacji serwera, który został znaleziony w kroku 4.
    * `appRoleId`: Identyfikator roli aplikacji uwidocznionej przez aplikację serwera, która została wygenerowana w kroku 5 — w tym przykładzie identyfikator roli aplikacji to `0566419e-bb95-4d9d-a4f8-ed9a0f147fa6` .
   
   Aby dodać przypisanie roli, wykonaj następujący skrypt programu PowerShell:

    ```powershell
    New-AzureADServiceAppRoleAssignment -ObjectId $managedIdentityObjectId -Id $appRoleId -PrincipalId $managedIdentityObjectId -ResourceId $serverServicePrincipalObjectId
    ```

## <a name="complete-script"></a>Kompletny skrypt

Ten przykładowy skrypt pokazuje, jak przypisać zarządzaną tożsamość aplikacji sieci Web platformy Azure do roli aplikacji.

```powershell
# Install the module. (You need admin on the machine.)
# Install-Module AzureAD

# Your tenant ID (in the Azure portal, under Azure Active Directory > Overview).
$tenantID = '<tenant-id>'

# The name of your web app, which has a managed identity that should be assigned to the server app's app role.
$webAppName = '<web-app-name>'
$resourceGroupName = '<resource-group-name-containing-web-app>'

# The name of the server app that exposes the app role.
$serverApplicationName = '<server-application-name>' # For example, MyApi

# The name of the app role that the managed identity should be assigned to.
$appRoleName = '<app-role-name>' # For example, MyApi.Read.All

# Look up the web app's managed identity's object ID.
$managedIdentityObjectId = (Get-AzWebApp -ResourceGroupName $resourceGroupName -Name $webAppName).identity.principalid

Connect-AzureAD -TenantId $tenantID

# Look up the details about the server app's service principal and app role.
$serverServicePrincipal = (Get-AzureADServicePrincipal -Filter "DisplayName eq '$serverApplicationName'")
$serverServicePrincipalObjectId = $serverServicePrincipal.ObjectId
$appRoleId = ($serverServicePrincipal.AppRoles | Where-Object {$_.Value -eq $appRoleName }).Id

# Assign the managed identity access to the app role.
New-AzureADServiceAppRoleAssignment `
    -ObjectId $managedIdentityObjectId `
    -Id $appRoleId `
    -PrincipalId $managedIdentityObjectId `
    -ResourceId $serverServicePrincipalObjectId
```

## <a name="next-steps"></a>Następne kroki

- [Tożsamość zarządzana dla zasobów platformy Azure — omówienie](overview.md)
- Aby włączyć zarządzaną tożsamość na maszynie wirtualnej platformy Azure, zobacz [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej platformy Azure przy użyciu programu PowerShell](qs-configure-powershell-windows-vm.md).
