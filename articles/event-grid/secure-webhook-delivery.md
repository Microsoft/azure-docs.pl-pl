---
title: Dostarczanie bezpiecznego elementu webhook za pomocą usługi Azure AD w Azure Event Grid
description: Opisuje sposób dostarczania zdarzeń do punktów końcowych HTTPS chronionych przez Azure Active Directory przy użyciu Azure Event Grid
ms.topic: how-to
ms.date: 03/20/2021
ms.openlocfilehash: 1298910db78ba468dd9744e84ee4629161e0a776
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076040"
---
# <a name="publish-events-to-azure-active-directory-protected-endpoints"></a>Publikowanie zdarzeń w punktach końcowych chronionych przez usługę Azure Active Directory
W tym artykule opisano sposób korzystania z Azure Active Directory (Azure AD) w celu zabezpieczenia połączenia między **subskrypcją zdarzeń** i **punktem końcowym elementu webhook**. Omówienie aplikacji usługi Azure AD i nazw głównych usług można znaleźć w temacie [Microsoft Identity platform (v 2.0) — Omówienie](../active-directory/develop/v2-overview.md).

W tym artykule jest używana Azure Portal dla demonstracji, jednak funkcja ta może być również włączona przy użyciu interfejsu wiersza polecenia, programu PowerShell lub zestawów SDK.


## <a name="create-an-azure-ad-application"></a>Tworzenie aplikacji usługi Azure AD
Zarejestruj element webhook w usłudze Azure AD, tworząc aplikację usługi Azure AD dla chronionego punktu końcowego. Zobacz [Scenariusz: chroniony internetowy interfejs API](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview). Skonfiguruj chroniony interfejs API do wywoływania przez aplikację demona.
    
## <a name="enable-event-grid-to-use-your-azure-ad-application"></a>Włącz Event Grid, aby korzystać z aplikacji usługi Azure AD
W tej sekcji przedstawiono sposób włączania Event Grid korzystania z aplikacji usługi Azure AD. 

> [!NOTE]
> Aby wykonać ten skrypt, musisz być członkiem [roli administratora aplikacji usługi Azure AD](../active-directory/roles/permissions-reference.md#all-roles) .

### <a name="connect-to-your-azure-tenant"></a>Nawiązywanie połączenia z dzierżawcą platformy Azure
Najpierw Połącz się z dzierżawą platformy Azure przy użyciu `Connect-AzureAD` polecenia. 

```PowerShell
$myWebhookAadTenantId = "<Your Webhook's Azure AD tenant id>"

Connect-AzureAD -TenantId $myWebhookAadTenantId
```

### <a name="create-microsofteventgrid-service-principal"></a>Utwórz nazwę główną usługi Microsoft. EventGrid
Uruchom następujący skrypt, aby utworzyć nazwę główną usługi dla **Microsoft. EventGrid** , jeśli jeszcze nie istnieje. 

```PowerShell
# This is the "Azure Event Grid" Azure Active Directory (AAD) AppId
$eventGridAppId = "4962773b-9cdb-44cf-a8bf-237846a00ab7"

# Create the "Azure Event Grid" AAD Application service principal if it doesn't exist
$eventGridSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventGridAppId + "'")
if ($eventGridSP -match "Microsoft.EventGrid")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the "Azure Event Grid" AAD Application and add it to the role
    Write-Host "Creating the Azure Event Grid service principal"
    $eventGridSP = New-AzureADServicePrincipal -AppId $eventGridAppId
}
```

### <a name="create-a-role-for-your-application"></a>Tworzenie roli dla aplikacji   
Uruchom następujący skrypt, aby utworzyć rolę dla aplikacji usługi Azure AD. W tym przykładzie nazwa roli to: **AzureEventGridSecureWebhookSubscriber**. Zmodyfikuj skrypt programu PowerShell `$myTenantId` , aby używał identyfikatora dzierżawy usługi Azure AD i `$myAzureADApplicationObjectId` identyfikatora obiektu aplikacji usługi Azure AD.

```PowerShell
# This is your Webhook's Azure AD Application's ObjectId. 
$myWebhookAadApplicationObjectId = "<Your webhook's aad application object id>"

# This is the name of the new role we will add to your Azure AD Application
$eventGridRoleName = "AzureEventGridSecureWebhookSubscriber"
       
# Create an application role of given name and description
Function CreateAppRole([string] $Name, [string] $Description)
{
    $appRole = New-Object Microsoft.Open.AzureAD.Model.AppRole
    $appRole.AllowedMemberTypes = New-Object System.Collections.Generic.List[string]
    $appRole.AllowedMemberTypes.Add("Application");
    $appRole.AllowedMemberTypes.Add("User");
    $appRole.DisplayName = $Name
    $appRole.Id = New-Guid
    $appRole.IsEnabled = $true
    $appRole.Description = $Description
    $appRole.Value = $Name;
    return $appRole
}
       
# Get my Azure AD Application, it's roles and service principal
$myApp = Get-AzureADApplication -ObjectId $myWebhookAadApplicationObjectId
$myAppRoles = $myApp.AppRoles

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
       
# Create the role if it doesn't exist
if ($myAppRoles -match $eventGridRoleName)
{
    Write-Host "The Azure Event Grid role is already defined.`n"
}
else
{      
    # Add our new role to the Azure AD Application
    Write-Host "Creating the Azure Event Grid role in Azure Ad Application: " $myWebhookAadApplicationObjectId
    $newRole = CreateAppRole -Name $eventGridRoleName -Description "Azure Event Grid Role"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}

# print application's roles
Write-Host "My Azure AD Application's Roles: "
Write-Host $myAppRoles

```

### <a name="create-a-role-assignment"></a>Utwórz przypisanie roli
Przypisanie roli należy utworzyć w aplikacja usługi Azure AD elementu webhook dla aplikacji usługi AAD lub użytkownika usługi AAD tworzącego subskrypcję zdarzeń. Użyj jednego ze skryptów poniżej w zależności od tego, czy aplikacja usługi AAD lub użytkownik usługi AAD tworzy subskrypcję zdarzeń.

#### <a name="option-a-create-a-role-assignment-for-event-subscription-aad-app"></a>Opcja A. Tworzenie przypisania roli dla aplikacji usługi AAD subskrypcji zdarzeń 

```powershell
# This is the app id of the application which will create event subscription. Set to $null if you are not assigning the role to app.
$eventSubscriptionWriterAppId = "<the app id of the application which will create event subscription>"

$myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")

$eventSubscriptionWriterSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventSubscriptionWriterAppId + "'")
if ($eventSubscriptionWriterSP -eq $null)
{
        $eventSubscriptionWriterSP = New-AzureADServicePrincipal -AppId $eventSubscriptionWriterAppId
}

Write-Host "Creating the Azure Ad App Role assignment for application: " $eventSubscriptionWriterAppId
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventSubscriptionWriterSP.ObjectId -PrincipalId $eventSubscriptionWriterSP.ObjectId
```

#### <a name="option-b-create-a-role-assignment-for-event-subscription-aad-user"></a>Opcja B. Tworzenie przypisania roli dla użytkownika usługi AAD subskrypcji zdarzeń 

```powershell
# This is the user principal name of the user who will create event subscription. Set to $null if you are not assigning the role to user.
$eventSubscriptionWriterUserPrincipalName = "<the user principal name of the user who will create event subscription>"

$myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
Write-Host "Creating the Azure Ad App Role assignment for user: " $eventSubscriptionWriterUserPrincipalName
$eventSubscriptionWriterUser = Get-AzureAdUser -ObjectId $eventSubscriptionWriterUserPrincipalName
New-AzureADUserAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventSubscriptionWriterUser.ObjectId -PrincipalId $eventSubscriptionWriterUser.ObjectId
```

### <a name="add-event-grid-service-principal-to-the-role"></a>Dodaj nazwę główną usługi Event Grid do roli
Uruchom New-AzureADServiceAppRoleAssignment polecenie, aby przypisać jednostkę usługi Event Grid do roli utworzonej w poprzednim kroku.

```powershell
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventGridSP.ObjectId -PrincipalId $eventGridSP.ObjectId
```

Uruchom następujące polecenia, aby uzyskać informacje wyjściowe, które będą używane później.

```powershell
Write-Host "My Webhook's Azure AD Tenant Id:  $myWebhookAadTenantId"
Write-Host "My Webhook's Azure AD Application Id: $($myApp.AppId)"
Write-Host "My Webhook's Azure AD Application ObjectId Id$($myApp.ObjectId)"
```

    
## <a name="configure-the-event-subscription"></a>Konfigurowanie subskrypcji zdarzeń
Podczas tworzenia subskrypcji zdarzeń wykonaj następujące kroki:

1. Wybierz typ punktu końcowego jako **element webhook**. 
1. Określ **Identyfikator URI** punktu końcowego.

    ![Wybierz element webhook typu punktu końcowego](./media/secure-webhook-delivery/select-webhook.png)
1. Wybierz kartę **dodatkowe funkcje** w górnej części strony **Tworzenie subskrypcji zdarzeń** .
1. Na karcie **funkcje dodatkowe** wykonaj następujące czynności:
    1. Wybierz opcję **Użyj uwierzytelniania usługi AAD** i skonfiguruj identyfikator dzierżawy oraz identyfikator aplikacji:
    1. Skopiuj identyfikator dzierżawy usługi Azure AD z danych wyjściowych skryptu i wprowadź go w polu **Identyfikator dzierżawy AAD** .
    1. Skopiuj identyfikator aplikacji Azure AD z danych wyjściowych skryptu i wprowadź go w polu **Identyfikator aplikacji usługi AAD** .

        ![Akcja bezpiecznego elementu webhook](./media/secure-webhook-delivery/aad-configuration.png)



## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje na temat monitorowania dostarczania zdarzeń, zobacz [monitorowanie Event Grid dostarczania komunikatów](monitor-event-delivery.md).
* Aby uzyskać więcej informacji na temat klucza uwierzytelniania, zobacz [Event Grid zabezpieczenia i uwierzytelnianie](security-authentication.md).
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji Azure Event Grid, zobacz [Event Grid schematu subskrypcji](subscription-creation-schema.md).
