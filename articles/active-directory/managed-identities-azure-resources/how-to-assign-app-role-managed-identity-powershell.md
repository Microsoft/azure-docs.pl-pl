---
title: Przypisywanie tożsamości zarządzanej do roli aplikacji przy użyciu programu PowerShell — Azure AD
description: Instrukcje krok po kroku dotyczące przypisywania dostępu tożsamości zarządzanej do roli innej aplikacji przy użyciu programu PowerShell.
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
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5150c26d67b77008c11764cc6e51ca7085ffcb31
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784915"
---
# <a name="assign-a-managed-identity-access-to-an-application-role-using-powershell"></a>Przypisywanie dostępu tożsamości zarządzanej do roli aplikacji przy użyciu programu PowerShell

Tożsamości zarządzane dla zasobów platformy Azure zapewniają usługom platformy Azure tożsamość w Azure Active Directory. Działają one bez konieczności poświadczeń w kodzie. Usługi platformy Azure używają tej tożsamości do uwierzytelniania w usługach, które obsługują uwierzytelnianie usługi Azure AD. Role aplikacji zapewniają formę kontroli dostępu opartej na rolach i umożliwiają usłudze implementowanie reguł autoryzacji.

Z tego artykułu dowiesz się, jak przypisać tożsamość zarządzaną do roli aplikacji udostępnianej przez inną aplikację przy użyciu programu Azure AD PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz informacji o tożsamościach zarządzanych dla zasobów platformy Azure, zapoznaj się z [sekcją przeglądu](overview.md). Zapoznaj się z różnicą między tożsamością zarządzaną przypisaną przez system i **[przypisaną przez użytkownika.](overview.md#managed-identity-types)**
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Aby uruchomić przykładowe skrypty, masz dwie opcje:
    - Użyj [Azure Cloud Shell](../../cloud-shell/overview.md), którą możesz otworzyć przy użyciu **przycisku Wypróbuj** w prawym górnym rogu bloków kodu.
    - Uruchamiaj skrypty lokalnie, instalując najnowszą wersję [programu Azure AD PowerShell.](/powershell/azure/active-directory/install-adv2)

## <a name="assign-a-managed-identity-access-to-another-applications-app-role"></a>Przypisywanie dostępu tożsamości zarządzanej do roli aplikacji innej aplikacji

1. Włącz tożsamość zarządzaną w zasobie platformy Azure, [takim jak maszyna wirtualna platformy Azure.](qs-configure-powershell-windows-vm.md)

1. Znajdź identyfikator obiektu jednostki usługi tożsamości zarządzanej.

   **W przypadku tożsamości zarządzanej przypisanej** przez system identyfikator obiektu można znaleźć Azure Portal na stronie **Tożsamość** zasobu. Możesz również użyć następującego skryptu programu PowerShell, aby znaleźć identyfikator obiektu. Będziesz potrzebować identyfikatora zasobu utworzonego w kroku 1, który jest dostępny w Azure Portal na **stronie Właściwości** zasobu.

    ```powershell
    $resourceIdWithManagedIdentity = '/subscriptions/{my subscription ID}/resourceGroups/{my resource group name}/providers/Microsoft.Compute/virtualMachines/{my virtual machine name}'
    (Get-AzResource -ResourceId $resourceIdWithManagedIdentity).Identity.PrincipalId
    ```

    **W przypadku tożsamości zarządzanej** przypisanej przez użytkownika identyfikator obiektu tożsamości zarządzanej można znaleźć w Azure Portal na stronie **Przegląd** zasobu. Możesz również użyć następującego skryptu programu PowerShell, aby znaleźć identyfikator obiektu. Będziesz potrzebować identyfikatora zasobu tożsamości zarządzanej przypisanej przez użytkownika.

    ```powershell
    $userManagedIdentityResourceId = '/subscriptions/{my subscription ID}/resourceGroups/{my resource group name}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{my managed identity name}'
    (Get-AzResource -ResourceId $userManagedIdentityResourceId).Properties.PrincipalId
    ```

1. Utwórz nową rejestrację aplikacji, aby reprezentować usługę, do która tożsamość zarządzana wyśle żądanie. Jeśli interfejs API lub usługa, która uwidacznia rolę aplikacji dla tożsamości zarządzanej, ma już jednostkę usługi w dzierżawie usługi Azure AD, pomiń ten krok. Jeśli na przykład chcesz udzielić tożsamości zarządzanej dostępu do interfejsu API Microsoft Graph, możesz pominąć ten krok.

1. Znajdź identyfikator obiektu jednostki usługi aplikacji usługi. Możesz to znaleźć przy użyciu Azure Portal. Przejdź do Azure Active Directory i otwórz stronę **Aplikacje dla** przedsiębiorstw, a następnie znajdź aplikację i poszukaj identyfikatora **obiektu**. Identyfikator obiektu jednostki usługi można również znaleźć za pomocą nazwy wyświetlanej, korzystając z następującego skryptu programu PowerShell:

    ```powershell
    $serverServicePrincipalObjectId = (Get-AzureADServicePrincipal -Filter "DisplayName eq '$applicationName'").ObjectId
    ```

    > [!NOTE]
    > Nazwy wyświetlane aplikacji nie są unikatowe, dlatego należy sprawdzić, czy została uzyskana prawidłowa nazwa główna usługi aplikacji.

1. Dodaj rolę [aplikacji do](../develop/howto-add-app-roles-in-azure-ad-apps.md) aplikacji utworzonej w kroku 3. Rolę można utworzyć przy użyciu Azure Portal lub Microsoft Graph. Możesz na przykład dodać rolę aplikacji w ten sposób:

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

1. Przypisz rolę aplikacji do tożsamości zarządzanej. Do przypisania roli aplikacji potrzebne są następujące informacje:
    * `managedIdentityObjectId`: identyfikator obiektu jednostki usługi tożsamości zarządzanej, który znajduje się w kroku 2.
    * `serverServicePrincipalObjectId`: identyfikator obiektu jednostki usługi aplikacji serwera znaleziony w kroku 4.
    * `appRoleId`: identyfikator roli aplikacji ujawniony przez aplikację serwera wygenerowany w kroku 5 — w tym przykładzie identyfikator roli aplikacji to `0566419e-bb95-4d9d-a4f8-ed9a0f147fa6` .
   
   Wykonaj następujący skrypt programu PowerShell, aby dodać przypisanie roli:

    ```powershell
    New-AzureADServiceAppRoleAssignment -ObjectId $managedIdentityObjectId -Id $appRoleId -PrincipalId $managedIdentityObjectId -ResourceId $serverServicePrincipalObjectId
    ```

## <a name="complete-script"></a>Kompletny skrypt

Ten przykładowy skrypt pokazuje, jak przypisać tożsamość zarządzaną aplikacji internetowej platformy Azure do roli aplikacji.

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

- [Omówienie tożsamości zarządzanej dla zasobów platformy Azure](overview.md)
- Aby włączyć tożsamość zarządzaną na maszynie wirtualnej platformy Azure, zobacz Configure managed identities for Azure resources on an Azure VM using PowerShell (Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure na maszynie [wirtualnej platformy Azure przy użyciu programu PowerShell).](qs-configure-powershell-windows-vm.md)
