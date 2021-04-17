---
title: Bezpieczne dostarczanie webhook za pomocą usługi Azure AD w usłudze Azure Event Grid
description: Opisuje sposób dostarczania zdarzeń do punktów końcowych HTTPS chronionych przez Azure Active Directory przy użyciu Azure Event Grid
ms.topic: how-to
ms.date: 04/13/2021
ms.openlocfilehash: 6a0f9059e17d96d497b425abc9749e69c5ab4d41
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575551"
---
# <a name="publish-events-to-azure-active-directory-protected-endpoints"></a>Publikowanie zdarzeń w punktach końcowych chronionych przez usługę Azure Active Directory
W tym artykule opisano, jak używać usługi Azure Active Directory (Azure  AD) do zabezpieczania połączenia między subskrypcją zdarzeń a **punktem końcowym urządzenia webhook.** Aby uzyskać omówienie aplikacji i jednostki usługi Azure AD, zobacz Omówienie platformy tożsamości firmy [Microsoft (wersja 2.0).](../active-directory/develop/v2-overview.md)

W tym artykule użyto Azure Portal pokazu, jednak tę funkcję można również włączyć przy użyciu interfejsu wiersza polecenia, programu PowerShell lub zestawów SDK.

> [!IMPORTANT]
> Dodatkowa kontrola dostępu została wprowadzona w ramach tworzenia lub aktualizowania subskrypcji zdarzeń w dniu 30 marca 2021 r. w celu rozwiązania luki w zabezpieczeniach. Jednostką usługi klienta subskrybenta musi być właściciel lub mieć przypisaną rolę w docelowej jednostki usługi aplikacji. Skonfiguruj ponownie aplikację WAD, zgodnie z nowymi instrukcjami poniżej.


## <a name="create-an-azure-ad-application"></a>Tworzenie aplikacji usługi Azure AD
Zarejestruj element webhook w usłudze Azure AD, tworząc aplikację usługi Azure AD dla chronionego punktu końcowego. Zobacz [Scenariusz: chroniony internetowy interfejs API](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview). Skonfiguruj chroniony interfejs API do wywoływania przez aplikację demona.
    
## <a name="enable-event-grid-to-use-your-azure-ad-application"></a>Włączanie Event Grid korzystania z aplikacji usługi Azure AD
W tej sekcji pokazano, jak włączyć Event Grid korzystania z aplikacji usługi Azure AD. 

> [!NOTE]
> Aby wykonać ten skrypt, musisz być członkiem roli administratora aplikacji usługi [Azure AD.](../active-directory/roles/permissions-reference.md#all-roles)

### <a name="connect-to-your-azure-tenant"></a>Nawiązywanie połączenia z dzierżawą platformy Azure
Najpierw połącz się z dzierżawą platformy Azure przy użyciu `Connect-AzureAD` polecenia . 

```PowerShell
$myWebhookAadTenantId = "<Your Webhook's Azure AD tenant id>"

Connect-AzureAD -TenantId $myWebhookAadTenantId
```

### <a name="create-microsofteventgrid-service-principal"></a>Tworzenie jednostki usługi Microsoft.EventGrid
Uruchom następujący skrypt, aby utworzyć jednostkę usługi **Dla Microsoft.EventGrid,** jeśli jeszcze nie istnieje. 

```PowerShell
# This is the "Azure Event Grid" Azure Active Directory (AAD) AppId
$eventGridAppId = "4962773b-9cdb-44cf-a8bf-237846a00ab7"

# Create the "Azure Event Grid" AAD Application service principal if it doesn't exist
$eventGridSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventGridAppId + "'")
if ($eventGridSP -match "Microsoft.EventGrid")
{
    Write-Host "The Service principal is already defined.`n"
} else {
    # Create a service principal for the "Azure Event Grid" AAD Application and add it to the role
    Write-Host "Creating the Azure Event Grid service principal"
    $eventGridSP = New-AzureADServicePrincipal -AppId $eventGridAppId
}
```

### <a name="create-a-role-for-your-application"></a>Tworzenie roli dla aplikacji   
Uruchom następujący skrypt, aby utworzyć rolę dla aplikacji usługi Azure AD. W tym przykładzie nazwa roli to **AzureEventGridSecureWebhookSubscriber.** Modyfikowanie skryptów programu PowerShell w celu użycia identyfikatora dzierżawy usługi Azure AD oraz identyfikatora obiektu `$myTenantId` `$myAzureADApplicationObjectId` aplikacji usługi Azure AD

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
} else {      
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

### <a name="create-role-assignment-for-the-client-creating-event-subscription"></a>Tworzenie przypisania roli dla klienta tworzącego subskrypcję zdarzeń
Przypisanie roli należy utworzyć w witrynie webhook aplikacja usługi Azure AD aplikacji usługi AAD lub użytkownika usługi AAD tworzącego subskrypcję zdarzeń. Użyj jednego z poniższych skryptów w zależności od tego, czy aplikacja usługi AAD, czy użytkownik usługi AAD tworzy subskrypcję zdarzeń.

> [!IMPORTANT]
> Dodatkowa kontrola dostępu została wprowadzona w ramach tworzenia lub aktualizowania subskrypcji zdarzeń w dniu 30 marca 2021 r. w celu rozwiązania luki w zabezpieczeniach. Jednostką usługi klienta subskrybenta musi być właściciel lub mieć przypisaną rolę w docelowej jednostki usługi aplikacji. Skonfiguruj ponownie aplikację AAD, zgodnie z nowymi instrukcjami poniżej.

#### <a name="create-role-assignment-for-an-event-subscription-aad-app"></a>Tworzenie przypisania roli dla aplikacji usługi AAD subskrypcji zdarzeń 

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
$eventGridAppRole = $myApp.AppRoles | Where-Object -Property "DisplayName" -eq -Value $eventGridRoleName
New-AzureADServiceAppRoleAssignment -Id $eventGridAppRole.Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventSubscriptionWriterSP.ObjectId -PrincipalId $eventSubscriptionWriterSP.ObjectId
```

#### <a name="create-role-assignment-for-an-event-subscription-aad-user"></a>Tworzenie przypisania roli dla użytkownika usługi AAD subskrypcji zdarzeń 

```powershell
# This is the user principal name of the user who will create event subscription. Set to $null if you are not assigning the role to user.
$eventSubscriptionWriterUserPrincipalName = "<the user principal name of the user who will create event subscription>"

$myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
Write-Host "Creating the Azure Ad App Role assignment for user: " $eventSubscriptionWriterUserPrincipalName
$eventSubscriptionWriterUser = Get-AzureAdUser -ObjectId $eventSubscriptionWriterUserPrincipalName
$eventGridAppRole = $myApp.AppRoles | Where-Object -Property "DisplayName" -eq -Value $eventGridRoleName
New-AzureADUserAppRoleAssignment -Id $eventGridAppRole.Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventSubscriptionWriterUser.ObjectId -PrincipalId $eventSubscriptionWriterUser.ObjectId
```

### <a name="create-role-assignment-for-event-grid-service-principal"></a>Tworzenie przypisania roli dla jednostki Event Grid Service
Uruchom polecenie New-AzureADServiceAppRoleAssignment , aby Event Grid jednostkę usługi do roli utworzonej w poprzednim kroku.

```powershell
$eventGridAppRole = $myApp.AppRoles | Where-Object -Property "DisplayName" -eq -Value $eventGridRoleName
New-AzureADServiceAppRoleAssignment -Id $eventGridAppRole.Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventGridSP.ObjectId -PrincipalId $eventGridSP.ObjectId
```

Uruchom następujące polecenia, aby uzyskać informacje wyjściowe, których użyjesz później.

```powershell
Write-Host "My Webhook's Azure AD Tenant Id:  $myWebhookAadTenantId"
Write-Host "My Webhook's Azure AD Application Id: $($myApp.AppId)"
Write-Host "My Webhook's Azure AD Application ObjectId Id$($myApp.ObjectId)"
```

    
## <a name="configure-the-event-subscription"></a>Konfigurowanie subskrypcji zdarzeń
Podczas tworzenia subskrypcji zdarzeń wykonaj następujące kroki:

1. Wybierz typ punktu końcowego Web **Hook**. 
1. Określ adres **URI punktu końcowego**.

    ![Wybieranie typu punktu końcowego webhook](./media/secure-webhook-delivery/select-webhook.png)
1. Wybierz **kartę Dodatkowe** funkcje w górnej części strony **Tworzenie subskrypcji** zdarzeń.
1. Na **karcie Dodatkowe funkcje** wykonaj następujące czynności:
    1. Wybierz **pozycję Użyj uwierzytelniania usługi AAD** i skonfiguruj identyfikator dzierżawy i identyfikator aplikacji:
    1. Skopiuj identyfikator dzierżawy usługi Azure AD z danych wyjściowych skryptu i wprowadź go w polu Identyfikator dzierżawy usługi **AAD.**
    1. Skopiuj identyfikator aplikacji usługi Azure AD z danych wyjściowych skryptu i wprowadź go w polu Identyfikator aplikacji **usługi AAD.** Alternatywnie możesz użyć identyfikatora URI identyfikatora aplikacji w u programie AAD. Aby uzyskać więcej informacji na temat identyfikatora URI aplikacji, zobacz [ten artykuł.](../app-service/configure-authentication-provider-aad.md)

        ![Akcja Zabezpieczanie webhook](./media/secure-webhook-delivery/aad-configuration.png)



## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje na temat monitorowania dostaw zdarzeń, [zobacz Monitorowanie Event Grid dostarczania komunikatów.](monitor-event-delivery.md)
* Aby uzyskać więcej informacji na temat klucza uwierzytelniania, [zobacz Event Grid zabezpieczeń i uwierzytelniania.](security-authentication.md)
* Aby uzyskać więcej informacji na temat tworzenia Azure Event Grid subskrypcji, zobacz [Event Grid schematu subskrypcji](subscription-creation-schema.md).
