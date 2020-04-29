---
title: Dostarczanie bezpiecznego elementu webhook za pomocą usługi Azure AD w Azure Event Grid
description: Opisuje sposób dostarczania zdarzeń do punktów końcowych HTTPS chronionych przez Azure Active Directory przy użyciu Azure Event Grid
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: babanisa
ms.openlocfilehash: 4cb8168cd6d1c19cc797a7cd5454b96131fa35be
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82106621"
---
# <a name="publish-events-to-azure-active-directory-protected-endpoints"></a>Publikuj zdarzenia w celu Azure Active Directory chronionych punktów końcowych

W tym artykule opisano, jak korzystać z Azure Active Directory, aby zabezpieczyć połączenie między subskrypcją zdarzeń a punktem końcowym elementu webhook. Omówienie aplikacji usługi Azure AD i nazw głównych usług można znaleźć w temacie [Microsoft Identity platform (v 2.0) — Omówienie](https://docs.microsoft.com/azure/active-directory/develop/v2-overview).

W tym artykule jest używana Azure Portal dla demonstracji, jednak funkcja ta może być również włączona przy użyciu interfejsu wiersza polecenia, programu PowerShell lub zestawów SDK.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-an-azure-ad-application"></a>Tworzenie aplikacji usługi Azure AD

Zacznij od utworzenia aplikacji usługi Azure AD dla chronionego punktu końcowego. Zobacz: https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview.
    - Skonfiguruj chroniony interfejs API do wywoływania przez aplikację demona.
    
## <a name="enable-event-grid-to-use-your-azure-ad-application"></a>Włącz Event Grid, aby korzystać z aplikacji usługi Azure AD

Użyj poniższego skryptu programu PowerShell, aby utworzyć regułę roli i usługi w aplikacji usługi Azure AD. Wymagany jest identyfikator dzierżawy i identyfikator obiektu z aplikacji usługi Azure AD:

    > [!NOTE]
    > You must be a member of the [Azure AD Application Administrator role](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles) to execute this script.
    
1. Zmodyfikuj $myTenantId skryptu programu PowerShell, aby użyć identyfikatora dzierżawy usługi Azure AD.
1. Zmodyfikuj $myAzureADApplicationObjectId skryptu programu PowerShell, aby użyć identyfikatora obiektu aplikacji usługi Azure AD
1. Uruchom zmodyfikowany skrypt.

```PowerShell
# This is your Tenant Id. 
$myTenantId = "<the Tenant Id of your Azure AD Application>"

Connect-AzureAD -TenantId $myTenantId
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
# This is the "Azure Event Grid" Azure Active Directory AppId
$eventGridAppId = "4962773b-9cdb-44cf-a8bf-237846a00ab7"
    
# This is the name of the new role we will add to your Azure AD Application
$eventGridRoleName = "AzureEventGridSecureWebhook"
    
# Create an application role of given name and description
Function CreateAppRole([string] $Name, [string] $Description)
{
    $appRole = New-Object Microsoft.Open.AzureAD.Model.AppRole
    $appRole.AllowedMemberTypes = New-Object System.Collections.Generic.List[string]
    $appRole.AllowedMemberTypes.Add("Application");
    $appRole.DisplayName = $Name
    $appRole.Id = New-Guid
    $appRole.IsEnabled = $true
    $appRole.Description = $Description
    $appRole.Value = $Name;
    return $appRole
}
    
# Get my Azure AD Application, it's roles and service principal
$myApp = Get-AzureADApplication -ObjectId $myAzureADApplicationObjectId
$myAppRoles = $myApp.AppRoles
$eventGridSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventGridAppId + "'")

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match $eventGridRoleName)
{
    Write-Host "The Azure Event Grid role is already defined.`n"
}
else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $eventGridRoleName -Description "Azure Event Grid Role"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}
    
# Create the service principal if it doesn't exist
if ($eventGridSP -match "Microsoft.EventGrid")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the "Azure Event Grid" Azure AD Application and add it to the role
    $eventGridSP = New-AzureADServicePrincipal -AppId $eventGridAppId
}
    
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventGridSP.ObjectId -PrincipalId $eventGridSP.ObjectId
    
Write-Host "My Azure AD Tenant Id: $myTenantId"
Write-Host "My Azure AD Application Id: $($myApp.AppId)"
Write-Host "My Azure AD Application ObjectId: $($myApp.ObjectId)"
Write-Host "My Azure AD Application's Roles: "
Write-Host $myApp.AppRoles
```
    
## <a name="configure-the-event-subscription"></a>Konfigurowanie subskrypcji zdarzeń

W przepływie tworzenia dla subskrypcji zdarzeń wybierz pozycję punkt końcowy "element webhook". Po otrzymaniu identyfikatora URI punktu końcowego kliknij kartę dodatkowe funkcje w górnej części bloku Tworzenie subskrypcji zdarzeń.

![Wybierz element webhook typu punktu końcowego](./media/secure-webhook-delivery/select-webhook.png)

Na karcie funkcje dodatkowe zaznacz pole wyboru Użyj uwierzytelniania usługi AAD, a następnie skonfiguruj identyfikator dzierżawy i identyfikator aplikacji:

* Skopiuj identyfikator dzierżawy usługi Azure AD z danych wyjściowych skryptu i wprowadź go w polu Identyfikator dzierżawy AAD.
* Skopiuj identyfikator aplikacji Azure AD z danych wyjściowych skryptu i wprowadź go w polu Identyfikator aplikacji usługi AAD.

    ![Akcja bezpiecznego elementu webhook](./media/secure-webhook-delivery/aad-configuration.png)

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje na temat monitorowania dostarczania zdarzeń, zobacz [monitorowanie Event Grid dostarczania komunikatów](monitor-event-delivery.md).
* Aby uzyskać więcej informacji na temat klucza uwierzytelniania, zobacz [Event Grid zabezpieczenia i uwierzytelnianie](security-authentication.md).
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji Azure Event Grid, zobacz [Event Grid schematu subskrypcji](subscription-creation-schema.md).
