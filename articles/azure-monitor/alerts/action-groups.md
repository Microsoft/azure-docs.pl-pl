---
title: Tworzenie grup akcji i zarządzanie nimi w witrynie Azure Portal
description: Dowiedz się, jak tworzyć grupy akcji i zarządzać nimi w Azure Portal.
author: dkamstra
ms.topic: conceptual
ms.date: 04/07/2021
ms.author: dukek
ms.openlocfilehash: 1486415c5d225163dd2b2c7e79cd008ad0a76588
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107514873"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Tworzenie grup akcji i zarządzanie nimi w witrynie Azure Portal
Grupa akcji to kolekcja preferencji powiadomień zdefiniowanych przez właściciela subskrypcji platformy Azure. Azure Monitor i Service Health alerty używają grup akcji do powiadamiania użytkowników o wyzwoleniu alertu. Różne alerty mogą używać tej samej grupy akcji lub różnych grup akcji w zależności od wymagań użytkownika. 

W tym artykule pokazano, jak tworzyć grupy akcji i zarządzać nimi w Azure Portal.

Każda akcja składa się z następujących właściwości:

* **Typ:** wykonane powiadomienie lub akcja. Przykłady obejmują wysyłanie połączeń głosowych, wiadomości SMS i wiadomości e-mail; lub wyzwalanie różnych typów akcji automatycznych. Zobacz typy w dalszej części tego artykułu.
* **Nazwa:** unikatowy identyfikator w grupie akcji.
* **Szczegóły:** odpowiednie szczegóły, które różnią się w zależności *od typu*.

Aby uzyskać informacje na temat konfigurowania grup akcji przy użyciu Azure Resource Manager akcji, zobacz Szablony grup akcji [Resource Manager akcji.](./action-groups-create-resource-manager-template.md)

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Tworzenie grupy akcji przy użyciu Azure Portal

1. Na stronie [Azure Portal](https://portal.azure.com)wyszukaj i wybierz pozycję **Monitoruj.** Okienko **Monitorowanie** konsoliduje wszystkie ustawienia monitorowania i dane w jednym widoku.

1. Wybierz **pozycję Alerty,** a następnie **wybierz pozycję Zarządzaj akcjami.**

    ![Przycisk Zarządzaj akcjami](./media/action-groups/manage-action-groups.png)
    
1. Wybierz **pozycję Dodaj grupę** akcji i wypełnij odpowiednie pola w interfejsie kreatora.

    ![Polecenie "Dodaj grupę akcji"](./media/action-groups/add-action-group.PNG)

### <a name="configure-basic-action-group-settings"></a>Konfigurowanie podstawowych ustawień grupy akcji

W **obszarze Project details (Szczegóły projektu):**

Wybierz **subskrypcję i** **grupę zasobów,** w której jest zapisywana grupa akcji.

W **obszarze Szczegóły wystąpienia:**

1. Wprowadź nazwę **grupy akcji**.

1. Wprowadź nazwę **wyświetlaną**. Nazwa wyświetlana jest używana w miejsce pełnej nazwy grupy akcji podczas wysłania powiadomień przy użyciu tej grupy.

      ![Okno dialogowe Dodawanie grupy akcji](./media/action-groups/action-group-1-basics.png)


### <a name="configure-notifications"></a>Konfigurowanie powiadomień

1. Kliknij przycisk **Dalej: powiadomienia >,** aby przejść do karty  **Powiadomienia,** lub wybierz kartę Powiadomienia w górnej części ekranu.

1. Zdefiniuj listę powiadomień do wysłania po wyzwoleniu alertu. Dla każdego powiadomienia podaj następujące informacje:

    a. **Typ powiadomienia:** wybierz typ powiadomienia, które chcesz wysłać. Dostępne opcje:
      * Rola Azure Resource Manager e-mail — wyślij wiadomość e-mail do użytkowników przypisanych do określonych ról usługi ARM na poziomie subskrypcji.
      * Poczta e-mail/wiadomość SMS/wypychanie/głos — wysyłaj te typy powiadomień do określonych adresatów.
    
    b. **Nazwa:** wprowadź unikatową nazwę powiadomienia.

    c. **Szczegóły:** na podstawie wybranego typu powiadomienia wprowadź adres e-mail, numer telefonu itp.
    
    d. **Wspólny schemat alertów:** możesz włączyć wspólny schemat [alertów](./alerts-common-schema.md), co zapewnia zaletę posiadania pojedynczego, rozszerzanego i ujednoliconego ładunku alertów we wszystkich usługach alertów w Azure Monitor.

    ![Karta Powiadomienia](./media/action-groups/action-group-2-notifications.png)
    
### <a name="configure-actions"></a>Konfigurowanie akcji

1. Kliknij przycisk **Dalej: >** akcje, aby  przejść na kartę Akcje, lub wybierz kartę Akcje w górnej części ekranu. 

1. Zdefiniuj listę akcji wyzwalanych po wyzwoleniu alertu. Dla każdej akcji podaj następujące informacje:

    a. **Typ akcji:** wybierz pozycję Automation Runbook, Azure Function, ITSM, Logic App, Secure Webhook, Webhook.
    
    b. **Nazwa:** wprowadź unikatową nazwę akcji.

    c. **Szczegóły:** na podstawie typu akcji wprowadź wartość URI obiektu webhook, aplikację platformy Azure, połączenie ITSM lub runbook usługi Automation. Dla akcji ITSM określ dodatkowo element pracy **i** inne pola wymagane przez narzędzie ITSM.
    
    d. **Wspólny schemat alertów:** możesz włączyć wspólny schemat [alertów,](./alerts-common-schema.md)który zapewnia przewagę nad pojedynczym rozszerzalnym i ujednoliconym ładunkiem alertów we wszystkich usługach alertów w Azure Monitor.
    
    ![Karta Akcje](./media/action-groups/action-group-3-actions.png)

### <a name="create-the-action-group"></a>Tworzenie grupy akcji

1. Jeśli chcesz, możesz przejrzeć ustawienia **Tagi**. Dzięki temu można skojarzyć pary klucz/wartość z grupą akcji w celu kategoryzacji i jest to funkcja dostępna dla dowolnego zasobu platformy Azure.

    ![Karta Tagi](./media/action-groups/action-group-4-tags.png)
    
1. Kliknij przycisk **Przejrzyj i utwórz**, aby przejrzeć ustawienia. Spowoduje to szybką weryfikację danych wejściowych, aby upewnić się, że wszystkie wymagane pola zostały wybrane. Jeśli występują problemy, zostaną zgłoszone w tym miejscu. Po przejrzenia ustawień kliknij przycisk Utwórz, **aby** aprowizować grupę akcji.
    
    ![Karta Przeglądanie i tworzenie](./media/action-groups/action-group-5-review.png)

> [!NOTE]
> Po skonfigurowaniu akcji powiadamiania osoby za pomocą poczty e-mail lub wiadomości SMS otrzyma potwierdzenie wskazujące, że została dodana do grupy akcji.

## <a name="manage-your-action-groups"></a>Zarządzanie grupami akcji

Po utworzeniu grupy akcji możesz  wyświetlić grupy  akcji, wybierając pozycję Zarządzaj akcjami na stronie docelowej **Alerty** w **okienku** Monitorowanie. Wybierz grupę akcji, którą chcesz zarządzać:

* Dodawanie, edytowanie lub usuwanie akcji.
* Usuń grupę akcji.

## <a name="action-specific-information"></a>Informacje specyficzne dla akcji

> [!NOTE]
> Zobacz [Subscription Service Limits for Monitoring (Limity](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-monitor-limits) usługi subskrypcji), aby uzyskać informacje o limitach liczbowych dla każdego z poniższych elementów.  

### <a name="automation-runbook"></a>Element runbook automatyzacji
Zapoznaj się z [limitami usługi subskrypcji platformy Azure,](../../azure-resource-manager/management/azure-subscription-service-limits.md) aby uzyskać informacje o limitach ładunków runbook.

W grupie akcji może być ograniczona liczba akcji runbook. 

### <a name="azure-app-push-notifications"></a>Powiadomienia wypychane aplikacji platformy Azure
Włącz wysyłanie powiadomień wypychanych [do aplikacja mobilna platformy Azure,](https://azure.microsoft.com/features/azure-portal/mobile-app/) podając adres e-mail, który będzie pełnić funkcję identyfikatora konta podczas konfigurowania aplikacja mobilna platformy Azure.

W grupie akcji może być ograniczona liczba akcji aplikacji platformy Azure.

### <a name="email"></a>E-mail
Wiadomości e-mail będą wysyłane z następujących adresów e-mail. Upewnij się, że filtrowanie wiadomości e-mail jest odpowiednio skonfigurowane
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

W grupie akcji może być ograniczona liczba akcji poczty e-mail. Zobacz artykuł [z informacjami o ograniczaniu szybkości.](./alerts-rate-limiting.md)

### <a name="email-azure-resource-manager-role"></a>Wyślij wiadomość e-mail do roli usługi Azure Resource Manager
Wyślij wiadomość e-mail do członków roli subskrypcji. Wiadomość e-mail będzie wysyłana tylko do członków roli użytkowników usługi **Azure AD.** Wiadomość e-mail nie zostanie wysłana do grup lub jednostek usługi Azure AD.

Wiadomość e-mail z powiadomieniem jest wysyłana tylko na *podstawowy adres e-mail.*

Jeśli nie otrzymujesz powiadomień dotyczących podstawowej poczty *e-mail,* możesz spróbować wykonać następujące czynności:

1. W Azure Portal przejdź do usługi *Active Directory.*
2. Kliknij pozycję Wszyscy użytkownicy (w okienku po lewej stronie), aby wyświetlić listę użytkowników (w okienku po prawej stronie).
3. Wybierz użytkownika, dla którego chcesz przejrzeć podstawowe *informacje e-mail.*

  :::image type="content" source="media/action-groups/active-directory-user-profile.png" alt-text="Przykład sposobu przeglądania profilu użytkownika." border="true":::

4. W obszarze Profil użytkownika w obszarze Informacje kontaktowe, jeśli karta "Adres  e-mail"  jest pusta, kliknij przycisk edytuj u góry i dodaj swój podstawowy adres e-mail, a następnie naciśnij przycisk Zapisz u góry. 

  :::image type="content" source="media/action-groups/active-directory-add-primary-email.png" alt-text="Przykład sposobu dodawania podstawowej poczty e-mail." border="true":::

W grupie akcji może być ograniczona liczba akcji poczty e-mail. Zobacz artykuł [z informacjami o ograniczaniu szybkości.](./alerts-rate-limiting.md)

Podczas konfigurowania roli *arm poczty* e-mail musisz upewnić się, że są spełnione poniżej 3 warunki:

1. Typem jednostki przypisanej do roli musi być **"Użytkownik".**
2. Przypisanie należy wykonać na **poziomie** subskrypcji.
3. Użytkownik musi mieć skonfigurowaną wiadomość e-mail w profilu **usługi AAD.** 


### <a name="function"></a>Funkcja
Wywołuje istniejący punkt końcowy wyzwalacza HTTP w [Azure Functions](../../azure-functions/functions-get-started.md). Aby obsłużyć żądanie, punkt końcowy musi obsługiwać zlecenie HTTP POST.

W grupie akcji może być ograniczona liczba akcji funkcji.

### <a name="itsm"></a>ITSM
Akcja ITSM wymaga połączenia ITSM. Dowiedz się, jak utworzyć [połączenie ITSM.](./itsmc-overview.md)

W grupie akcji może być ograniczona liczba akcji ITSM. 

### <a name="logic-app"></a>Aplikacja logiki
W grupie akcji może być ograniczona liczba akcji aplikacji logiki.

### <a name="secure-webhook"></a>Bezpieczny element webhook
Akcja Zabezpieczanie urządzenia webhook grup akcji umożliwia korzystanie z usługi Azure Active Directory w celu zabezpieczenia połączenia między grupą akcji a chronionym internetowym interfejsem API (punktem końcowym). Ogólny przepływ pracy do korzystania z tej funkcji został opisany poniżej. Aby uzyskać omówienie aplikacji usługi Azure AD i jednostki usługi, zobacz Omówienie platformy tożsamości firmy [Microsoft (wersja 2.0).](../../active-directory/develop/v2-overview.md)

> [!NOTE]
> Użycie akcji element webhook wymaga, aby docelowy punkt końcowy element webhook nie wymagał szczegółów alertu do pomyślnego działania lub był w stanie analizowania informacji o kontekście alertu dostarczanych w ramach operacji POST. Jeśli punkt końcowy webhook nie może samodzielnie obsługiwać informacji o kontekście [](./action-groups-logic-app.md) alertu, możesz użyć rozwiązania, takiego jak akcja aplikacji logiki, do niestandardowego manipulowania informacjami o kontekście alertu w celu dopasowania go do oczekiwanego formatu danych.

1. Utwórz aplikację usługi Azure AD dla chronionego internetowego interfejsu API. Zobacz [Protected web API: App registration (Chroniony internetowy interfejs API: rejestracja aplikacji).](../../active-directory/develop/scenario-protected-web-api-app-registration.md)
    - Skonfiguruj chroniony interfejs API do [wywoływania przez aplikację demona](../../active-directory/develop/scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app).
    
2. Włącz grupy akcji, aby używać aplikacji usługi Azure AD.

    > [!NOTE]
    > Aby wykonać ten skrypt, musisz być członkiem roli administratora aplikacji usługi [Azure AD.](../../active-directory/roles/permissions-reference.md#all-roles)
    
    - Zmodyfikuj wywołanie Connect-AzureAD programu PowerShell, aby użyć identyfikatora dzierżawy usługi Azure AD.
    - Zmodyfikuj zmienną skryptu programu PowerShell$myAzureADApplicationObjectId aby użyć identyfikatora obiektu aplikacji usługi Azure AD.
    - Uruchom zmodyfikowany skrypt.
    
3. Skonfiguruj akcję Zabezpieczanie urządzenia webhook grupy akcji.
    - Skopiuj wartość $myApp.ObjectId ze skryptu i wprowadź ją w polu Identyfikator obiektu aplikacji w definicji akcji webhook.
    
    ![Akcja Zabezpieczanie webhook](./media/action-groups/action-groups-secure-webhook.png)

#### <a name="secure-webhook-powershell-script"></a>Bezpieczny skrypt programu PowerShell dla webhook

```PowerShell
Connect-AzureAD -TenantId "<provide your Azure AD tenant ID here>"
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object ID of your Azure AD Application>"
    
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
    
Write-Host "My Azure AD Application (ObjectId): " + $myApp.ObjectId
Write-Host "My Azure AD Application's Roles"
Write-Host $myApp.AppRoles
```

### <a name="sms"></a>SMS
Aby uzyskać [dodatkowe ważne informacje, zobacz](./alerts-rate-limiting.md) informacje o ograniczaniu szybkości i [zachowaniu alertów](./alerts-sms-behavior.md) SMS. 

W grupie akcji może być ograniczona liczba akcji sms.

> [!NOTE]
> Jeśli interfejs Azure Portal grupy akcji nie pozwala wybrać kodu kraju/regionu, wiadomość SMS nie jest obsługiwana w Twoim kraju/regionie.  Jeśli kod kraju/regionu jest niedostępny, możesz zagłosować, aby dodać swój kraj/region pod [głosem użytkownika.](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice) W międzyczasie należy wywołać przez grupę akcji webhook do dostawcy programu SMS innej firmy z pomocą techniczną w Twoim kraju/regionie.  

Cennik obsługiwanych krajów/regionów znajduje się na Azure Monitor [cennika.](https://azure.microsoft.com/pricing/details/monitor/)

**Lista krajów, w których jest obsługiwane powiadomienie SMS**

| Kod kraju | Nazwa kraju |
|:---|:---|
| 61 | Australia |
| 43 | Austria |
| 32 | Belgia |
| 55 | Brazylia |
| 1 |Kanada |
| 56 | Chile |
| 86 | Chiny |
| 420 | Republika Czeska |
| 45 | Dania |
| 372 | Estonia |
| 358 | Finlandia |
| 33 | Francja |
| 49 | Niemcy |
| 852 | Hongkong |
| 91 | Indie |
| 353 | Irlandia |
| 972 | Izrael |
| 39 | Włochy |
| 81 | Japonia |
| 352 | Luksemburg |
| 60 | Malezja |
| 52 | Meksyk |
| 31 | Holandia |
| 64 | Nowa Zelandia |
| 47 | Norwegia |
| 351 | Portugalia |
| 1 | Portoryko |
| 40 | Rumunia |
| 65 | Singapur |
| 27 | Republika Południowej Afryki |
| 82 | Korea Południowa |
| 34 | Hiszpania |
| 41 | Szwajcaria |
| 886 | Tajwan |
| 44 | Zjednoczone Królestwo |
| 1 | Stany Zjednoczone |

### <a name="voice"></a>Połączenia głosowe
Zobacz artykuł [z informacjami o ograniczaniu szybkości,](./alerts-rate-limiting.md) aby uzyskać dodatkowe ważne zachowanie.

W grupie akcji może być ograniczona liczba akcji głosowych.

> [!NOTE]
> Jeśli interfejs Azure Portal grupy akcji nie pozwala wybrać kodu kraju/regionu, połączenia głosowe nie są obsługiwane w Twoim kraju/regionie. Jeśli kod kraju/regionu jest niedostępny, możesz zagłosować, aby dodać kraj/region pod [głosem użytkownika](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice).  W międzyczasie praca jest tak, aby grupa akcji dzwoniła do zewnętrznego dostawcy połączeń głosowych z pomocą techniczną w Twoim kraju/regionie.  
> Obecnie tylko kod kraju obsługiwany w Azure Portal akcji dla powiadomień głosowych to +1(Stany Zjednoczone). 

Cennik obsługiwanych krajów/regionów znajduje się na Azure Monitor [cennika.](https://azure.microsoft.com/pricing/details/monitor/)

### <a name="webhook"></a>Webhook

> [!NOTE]
> Użycie akcji element webhook wymaga, aby docelowy punkt końcowy element webhook nie wymagał szczegółów alertu do pomyślnego działania lub był w stanie analizujeć informacje o kontekście alertu, które są dostarczane w ramach operacji POST. Jeśli punkt końcowy webhook nie może samodzielnie obsłużyć informacji o kontekście alertu, możesz użyć rozwiązania, takiego jak akcja aplikacji logiki, do niestandardowego manipulowania informacjami o kontekście alertu w celu dopasowania go do oczekiwanego formatu danych. [](./action-groups-logic-app.md)

Elementy webhook są przetwarzane przy użyciu następujących reguł
- Próba wywołania webhook jest podejmowana maksymalnie 3 razy.
- Wywołanie zostanie ponoowane, jeśli odpowiedź nie zostanie odebrana w ramach limitu czasu lub zostanie zwrócony jeden z następujących kodów stanu HTTP: 408, 429, 503 lub 504.
- Pierwsze wywołanie zaczeka 10 sekund na odpowiedź.
- Druga i trzecia próba zaczeka 30 sekund na odpowiedź.
- Po 3 próbach wywołania tego webhook żadna grupa akcji nie wywoła punktu końcowego przez 15 minut.

Zobacz Adresy [IP grupy akcji dla](../app/ip-addresses.md) zakresów źródłowych adresów IP.


## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [zachowaniu alertów SMS.](./alerts-sms-behavior.md)  
* Uzyskaj informacje [na temat schematu element webhook alertu dziennika aktywności](./activity-log-alerts-webhook.md).  
* Dowiedz się więcej o [łącznik ITSM](./itsmc-overview.md).
* Dowiedz się więcej na [temat ograniczania liczby alertów.](./alerts-rate-limiting.md)
* Zapoznaj się [z omówieniem alertów dziennika aktywności](./alerts-overview.md)i dowiedz się, jak otrzymywać alerty.  
* Dowiedz się, [jak skonfigurować alerty za każdym razem, gdy zostanie opublikowane powiadomienie o kondycji usługi.](../../service-health/alerts-activity-log-service-notifications-portal.md)
