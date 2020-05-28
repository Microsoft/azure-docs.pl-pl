---
title: Tworzenie grup akcji i zarządzanie nimi w Azure Portal
description: Dowiedz się, jak tworzyć grupy akcji i zarządzać nimi w Azure Portal.
author: dkamstra
ms.topic: conceptual
ms.date: 4/17/2020
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 8075574556375b7c07de2abd6c5aff792880b497
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83738822"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Tworzenie grup akcji i zarządzanie nimi w Azure Portal
Grupa akcji to zbiór preferencji powiadomień definiowanych przez właściciela subskrypcji platformy Azure. Alerty Azure Monitor i Service Health umożliwiają Powiadamianie użytkowników o wyzwoleniu alertu. Różne alerty mogą korzystać z tej samej grupy akcji lub różnych grup akcji w zależności od wymagań użytkownika. W ramach subskrypcji można skonfigurować maksymalnie 2 000 grup akcji.

Użytkownik konfiguruje akcję w celu powiadomienia osoby za pośrednictwem poczty e-mail lub wiadomości SMS otrzymuje potwierdzenie wskazujące, że zostały dodane do grupy akcji.

W tym artykule przedstawiono sposób tworzenia grup akcji i zarządzania nimi w Azure Portal.

Każda akcja składa się z następujących właściwości:

* **Nazwa**: unikatowy identyfikator w ramach grupy akcji.  
* **Typ akcji**: wykonaną akcję. Przykładami mogą być wysyłanie połączeń głosowych, wiadomości SMS i poczty e-mail; lub wyzwalając różne typy zautomatyzowanych akcji. Zobacz typy w dalszej części tego artykułu.
* **Szczegóły**: odpowiednie szczegóły, które różnią się w zależności od *typu akcji*.

Aby uzyskać informacje na temat sposobu konfigurowania grup akcji przy użyciu szablonów Azure Resource Manager, zobacz [Group action Menedżer zasobów templates](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Tworzenie grupy akcji przy użyciu Azure Portal

1. W [Azure Portal](https://portal.azure.com)Wyszukaj i wybierz pozycję **monitor**. Okienko **monitorowanie** konsoliduje wszystkie ustawienia monitorowania i dane w jednym widoku.

1. Wybierz pozycję **Alerty**, a następnie wybierz pozycję **Zarządzaj akcjami**.

    ![Przycisk zarządzania akcjami](./media/action-groups/manage-action-groups.png)
    
1. Wybierz pozycję **Dodaj grupę akcji**i wypełnij pola.

    ![Polecenie "Dodaj grupę akcji"](./media/action-groups/add-action-group.png)
    
1. Wprowadź nazwę w polu **Nazwa grupy akcji** , a następnie wprowadź nazwę w polu **krótka nazwa** . Nazwa krótka jest używana zamiast pełnej nazwy grupy akcji podczas przesyłania powiadomień przy użyciu danej grupy.

      ![Okno dialogowe Dodawanie grupy akcji](./media/action-groups/action-group-define.png)

1. Pole **subskrypcji** wypełnia bieżącą subskrypcję. Ta subskrypcja jest taka, w której jest zapisywana grupa akcji.

1. Wybierz **grupę zasobów** , w której jest zapisywana grupa akcji.

1. Zdefiniuj listę akcji. Dla każdej akcji podaj następujące elementy:

    1. **Nazwa**: wprowadź unikatowy identyfikator dla tej akcji.

    1. **Typ akcji**: Wybierz element Runbook usługi Automation, funkcję platformy Azure, wiadomość E-mail Azure Resource Manager rolę, wiadomość E-mail/SMS/wypychanie/głos, narzędzia ITSM, Logic App, Secure Hook, webhook.

    1. **Szczegóły**: w zależności od typu akcji wprowadź numer telefonu, adres e-mail, identyfikator URI elementu webhook, aplikację platformy Azure, połączenie narzędzia ITSM lub element Runbook usługi Automation. Dla akcji narzędzia ITSM należy dodatkowo określić **element roboczy** i inne pola wymagane przez narzędzie narzędzia ITSM.
    
    1. **Wspólny schemat alertów**: można włączyć [wspólny schemat alertów](https://aka.ms/commonAlertSchemaDocs), który umożliwia korzystanie z jednego rozszerzalnego i ujednoliconego ładunku alertów dla wszystkich usług alertów w Azure monitor.

1. Wybierz **przycisk OK** , aby utworzyć grupę akcji.

## <a name="manage-your-action-groups"></a>Zarządzanie grupami akcji

Po utworzeniu grupy akcji można wyświetlić **grupy akcji** , wybierając pozycję **Zarządzaj akcjami** na stronie miejsce docelowe **alertów** w okienku **monitorowanie** . Wybierz grupę akcji, do której chcesz zarządzać:

* Dodawanie, edytowanie lub usuwanie akcji.
* Usuń grupę akcji.

## <a name="action-specific-information"></a>Informacje dotyczące akcji

> [!NOTE]
> Zobacz [limity usługi subskrypcji, aby monitorować](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-monitor-limits) limity liczbowe dla każdego z poniższych elementów.  

### <a name="automation-runbook"></a>Element Runbook usługi Automation
Limity dotyczące ładunków elementów Runbook można znaleźć w [limitach usługi subskrypcji platformy Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md) .

W grupie akcji może istnieć ograniczona liczba akcji elementu Runbook. 

### <a name="azure-app-push-notifications"></a>Powiadomienia push aplikacji platformy Azure
W grupie akcji może istnieć ograniczona liczba akcji aplikacji platformy Azure.

### <a name="email"></a>Adres e-mail
Wiadomości e-mail będą wysyłane z następujących adresów e-mail. Upewnij się, że filtrowanie poczty e-mail jest skonfigurowane odpowiednio
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

W grupie akcji może istnieć ograniczona liczba akcji poczty e-mail. Zobacz artykuł [Informacje o ograniczeniu szybkości](./../../azure-monitor/platform/alerts-rate-limiting.md) .

### <a name="email-azure-resource-manager-role"></a>Wyślij wiadomość e-mail do roli usługi Azure Resource Manager
Wyślij wiadomość e-mail do członków roli subskrypcji. Poczta e-mail będzie wysyłana tylko do członków roli **użytkownika usługi Azure AD** . Wiadomość e-mail nie zostanie wysłana do grup lub jednostek usługi Azure AD.

W grupie akcji może istnieć ograniczona liczba akcji poczty e-mail. Zobacz artykuł [Informacje o ograniczeniu szybkości](./../../azure-monitor/platform/alerts-rate-limiting.md) .

### <a name="function"></a>Funkcja
Wywołuje istniejący punkt końcowy wyzwalacza HTTP w [Azure Functions](../../azure-functions/functions-create-first-azure-function.md#create-a-function-app).

W grupie akcji może istnieć ograniczona liczba akcji funkcji.

### <a name="itsm"></a>ITSM
Akcja narzędzia ITSM wymaga połączenia narzędzia ITSM. Dowiedz się, jak utworzyć [połączenie narzędzia ITSM](../../azure-monitor/platform/itsmc-overview.md).

W grupie akcji może istnieć ograniczona liczba akcji narzędzia ITSM. 

### <a name="logic-app"></a>Aplikacja logiki
W grupie akcji może istnieć ograniczona liczba akcji aplikacji logiki.

### <a name="secure-webhook"></a>Zabezpieczanie elementu webhook
Akcja elementu webhook grup akcji umożliwia korzystanie z Azure Active Directory w celu zabezpieczenia połączenia między grupą akcji i chronionym internetowym interfejsem API (punkt końcowy elementu webhook). Poniżej opisano ogólny przepływ pracy w celu skorzystania z zalet tej funkcji. Omówienie aplikacji usługi Azure AD i nazw głównych usług można znaleźć w temacie [Microsoft Identity platform (v 2.0) — Omówienie](https://docs.microsoft.com/azure/active-directory/develop/v2-overview).

1. Utwórz aplikację usługi Azure AD dla chronionego internetowego interfejsu API. Zobacz: https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview.
    - Skonfiguruj chroniony interfejs API do wywoływania przez aplikację demona.
    
1. Włącz grupy akcji, aby korzystać z aplikacji usługi Azure AD.

    > [!NOTE]
    > Aby wykonać ten skrypt, musisz być członkiem [roli administratora aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles) .
    
    - Zmodyfikuj wywołanie Connect-AzureAD skryptu programu PowerShell, aby użyć identyfikatora dzierżawy usługi Azure AD.
    - Zmodyfikuj zmienną $myAzureADApplicationObjectId skryptu programu PowerShell, aby użyć identyfikatora obiektu aplikacji usługi Azure AD
    - Uruchom zmodyfikowany skrypt.
    
1. Skonfiguruj akcję bezpiecznego elementu webhook grupy akcji.
    - Skopiuj wartość $myApp. ObjectId ze skryptu i wprowadź ją w polu Identyfikator obiektu aplikacji w definicji akcji elementu webhook.
    
    ![Akcja bezpiecznego elementu webhook](./media/action-groups/action-groups-secure-webhook.png)

#### <a name="secure-webhook-powershell-script"></a>Bezpieczny skrypt programu PowerShell elementu webhook

```PowerShell
Connect-AzureAD -TenantId "<provide your Azure AD tenant ID here>"
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
# This is the Action Groups Azure AD AppId
$actionGroupsAppId = "461e8683-5575-4561-ac7f-899cc907d62a"
    
# This is the name of the new role we will add to your Azure AD Application
$actionGroupRoleName = "ActionGroupsSecureWebhook"
    
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
$actionGroupsSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $actionGroupsAppId + "'")

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match "ActionGroupsSecureWebhook")
{
    Write-Host "The Action Groups role is already defined.`n"
}
else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $actionGroupRoleName -Description "This is a role for Action Groups to join"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}
    
# Create the service principal if it doesn't exist
if ($actionGroupsSP -match "AzNS AAD Webhook")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the Action Groups Azure AD Application and add it to the role
    $actionGroupsSP = New-AzureADServicePrincipal -AppId $actionGroupsAppId
}
    
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $actionGroupsSP.ObjectId -PrincipalId $actionGroupsSP.ObjectId
    
Write-Host "My Azure AD Application ($myApp.ObjectId): " + $myApp.ObjectId
Write-Host "My Azure AD Application's Roles"
Write-Host $myApp.AppRoles
```

### <a name="sms"></a>SMS
Aby uzyskać dodatkowe informacje, zobacz [informacje dotyczące ograniczania szybkości](./../../azure-monitor/platform/alerts-rate-limiting.md) oraz [zachowanie alertu programu SMS](../../azure-monitor/platform/alerts-sms-behavior.md) . 

W grupie akcji może istnieć ograniczona liczba akcji programu SMS.

> [!NOTE]
> Jeśli interfejs użytkownika grupy akcji Azure Portal nie pozwala na wybranie kodu kraju/regionu, to wiadomość SMS nie jest obsługiwana w Twoim kraju/regionie.  Jeśli kod kraju/regionu nie jest dostępny, możesz głosować na to, aby Twój kraj/region został dodany przy [głosowaniu użytkownika](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice). W międzyczasie obejście problemu polega na tym, że grupa akcji wywoła element webhook do dostawcy programu SMS innej firmy z obsługą w Twoim kraju/regionie.  

Cennik dla obsługiwanych krajów/regionów znajduje się na [stronie cennika Azure monitor](https://azure.microsoft.com/pricing/details/monitor/).
  

### <a name="voice"></a>Połączenia głosowe
Zapoznaj się z artykułem [dotyczącym ograniczania szybkości](./../../azure-monitor/platform/alerts-rate-limiting.md) , aby uzyskać dodatkowe ważne zachowanie.

W grupie akcji może istnieć ograniczona liczba akcji głosowych.

> [!NOTE]
> Jeśli interfejs użytkownika grupy akcji Azure Portal nie pozwala na wybranie kodu kraju/regionu, połączenia głosowe nie są obsługiwane w Twoim kraju/regionie. Jeśli kod kraju/regionu nie jest dostępny, możesz głosować na to, aby Twój kraj/region został dodany przy [głosowaniu użytkownika](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice).  Tymczasem obejście jest to, że grupa akcji wywoła element webhook do dostawcy wywołań głosowych innej firmy z obsługą w Twoim kraju/regionie.  

Cennik dla obsługiwanych krajów/regionów znajduje się na [stronie cennika Azure monitor](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="webhook"></a>Webhook
Próby elementów webhook zostały ponowione przy użyciu następujących reguł. Wywołanie elementu webhook jest ponawiane maksymalnie 2 razy, gdy zwracane są następujące kody stanu HTTP: 408, 429, 503, 504 lub punkt końcowy HTTP nie odpowiada. Pierwsze ponowienie próby odbywa się po 10 sekundach. Druga ponowna próba nastąpi po 100 sekundach. Po dwóch awariach żadna grupa akcji nie będzie wywoływała punktu końcowego przez 30 minut. 

Zakresy źródłowych adresów IP
 - 13.72.19.232
 - 13.106.57.181
 - 13.106.54.3
 - 13.106.54.19
 - 13.106.38.142
 - 13.106.38.148
 - 13.106.57.196
 - 13.106.57.197
 - 52.244.68.117
 - 52.244.65.137
 - 52.183.31.0
 - 52.184.145.166
 - 51.4.138.199
 - 51.5.148.86
 - 51.5.149.19

Aby otrzymywać aktualizacje dotyczące zmian wprowadzonych w tych adresach IP, zalecamy skonfigurowanie alertu Service Health, który monitoruje powiadomienia informacyjne o usłudze grup akcji.

W grupie akcji może istnieć ograniczona liczba akcji elementu webhook.



## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej na temat [zachowania alertu programu SMS](../../azure-monitor/platform/alerts-sms-behavior.md).  
* Uzyskaj [informacje na temat schematu elementu webhook alertu dziennika aktywności](../../azure-monitor/platform/activity-log-alerts-webhook.md).  
* Dowiedz się więcej o [Łącznik ITSM](../../azure-monitor/platform/itsmc-overview.md)
* Dowiedz się więcej o [ograniczaniu](../../azure-monitor/platform/alerts-rate-limiting.md) liczby alertów.
* Zapoznaj się z [omówieniem alertów dziennika aktywności](../../azure-monitor/platform/alerts-overview.md)i Dowiedz się, jak otrzymywać alerty.  
* Informacje o sposobie [konfigurowania alertów za każdym razem, gdy jest ogłaszane powiadomienie o kondycji usługi](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).
