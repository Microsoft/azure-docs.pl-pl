---
title: Tworzenie grup akcji i zarządzanie nimi w witrynie Azure Portal
description: Dowiedz się, jak tworzyć grupy akcji i zarządzać nimi w Azure Portal.
author: dkamstra
ms.topic: conceptual
ms.date: 02/25/2021
ms.author: dukek
ms.openlocfilehash: fb067e603c181482a863dc9fd75556e32a801bc6
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104772352"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Tworzenie grup akcji i zarządzanie nimi w witrynie Azure Portal
Grupa akcji to zbiór preferencji powiadomień definiowanych przez właściciela subskrypcji platformy Azure. Alerty Azure Monitor i Service Health umożliwiają Powiadamianie użytkowników o wyzwoleniu alertu. Różne alerty mogą korzystać z tej samej grupy akcji lub różnych grup akcji w zależności od wymagań użytkownika. 

W tym artykule przedstawiono sposób tworzenia grup akcji i zarządzania nimi w Azure Portal.

Każda akcja składa się z następujących właściwości:

* **Typ**: powiadomienie lub akcja wykonane. Przykładami mogą być wysyłanie połączeń głosowych, wiadomości SMS i poczty e-mail; lub wyzwalając różne typy zautomatyzowanych akcji. Zobacz typy w dalszej części tego artykułu.
* **Nazwa**: unikatowy identyfikator w ramach grupy akcji.
* **Szczegóły**: odpowiednie szczegóły, które różnią się w zależności od *typu*.

Aby uzyskać informacje na temat sposobu konfigurowania grup akcji przy użyciu szablonów Azure Resource Manager, zobacz [Group action Menedżer zasobów templates](./action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Tworzenie grupy akcji przy użyciu Azure Portal

1. W [Azure Portal](https://portal.azure.com)Wyszukaj i wybierz pozycję **monitor**. Okienko **monitorowanie** konsoliduje wszystkie ustawienia monitorowania i dane w jednym widoku.

1. Wybierz pozycję **alerty**, a następnie wybierz pozycję **Zarządzaj akcjami**.

    ![Przycisk zarządzania akcjami](./media/action-groups/manage-action-groups.png)
    
1. Wybierz pozycję **Dodaj grupę akcji** i Wypełnij odpowiednie pola w oknie środowisko kreatora.

    ![Polecenie "Dodaj grupę akcji"](./media/action-groups/add-action-group.PNG)

### <a name="configure-basic-action-group-settings"></a>Skonfiguruj podstawowe ustawienia grupy akcji

W obszarze **szczegóły projektu**:

Wybierz **subskrypcję** i **grupę zasobów** , w której jest zapisywana grupa akcji.

W obszarze **szczegóły wystąpienia**:

1. Wprowadź **nazwę grupy akcji**.

1. Wprowadź **nazwę wyświetlaną**. Nazwa wyświetlana jest używana zamiast pełnej nazwy grupy akcji, gdy powiadomienia są wysyłane przy użyciu tej grupy.

      ![Okno dialogowe Dodawanie grupy akcji](./media/action-groups/action-group-1-basics.png)


### <a name="configure-notifications"></a>Konfigurowanie powiadomień

1. Kliknij przycisk **Dalej: powiadomienia >** , aby przejść do karty **powiadomienia** , lub wybierz kartę **powiadomienia** u góry ekranu.

1. Zdefiniuj listę powiadomień do wysłania w przypadku wyzwolenia alertu. Dla każdego powiadomienia podaj następujące elementy:

    a. **Typ powiadomienia**: Wybierz typ powiadomienia, które chcesz wysłać. Dostępne opcje:
      * Azure Resource Manager rolę e-mail — Wyślij wiadomość e-mail do użytkowników przypisanych do określonych ról ARM na poziomie subskrypcji.
      * Poczta e-mail/wiadomość SMS/wypychanie/głos — wysyłaj te typy powiadomień do określonych adresatów.
    
    b. **Nazwa**: Wprowadź unikatową nazwę powiadomienia.

    c. **Szczegóły**: na podstawie wybranego typu powiadomienia wprowadź adres e-mail, numer telefonu itp.
    
    d. **Wspólny schemat alertów**: można włączyć [wspólny schemat alertów](./alerts-common-schema.md), który umożliwia korzystanie z jednego rozszerzalnego i ujednoliconego ładunku alertów dla wszystkich usług alertów w Azure monitor.

    ![Karta powiadomienia](./media/action-groups/action-group-2-notifications.png)
    
### <a name="configure-actions"></a>Skonfiguruj akcje

1. Kliknij przycisk **Dalej: działania >** , aby przejść do karty **Akcje** , lub wybierz kartę **Akcje** w górnej części ekranu.

1. Zdefiniuj listę akcji do wyzwolenia po wyzwoleniu alertu. Dla każdej akcji podaj następujące elementy:

    a. **Typ akcji**: Wybierz element Runbook usługi Automation, funkcja platformy Azure, narzędzia ITSM, aplikację logiki, bezpieczny element webhook, element webhook.
    
    b. **Nazwa**: Wprowadź unikatową nazwę dla akcji.

    c. **Szczegóły**: na podstawie typu akcji wprowadź identyfikator URI elementu webhook, aplikację platformy Azure, połączenie narzędzia ITSM lub element Runbook usługi Automation. Dla akcji narzędzia ITSM należy dodatkowo określić **element roboczy** i inne pola wymagane przez narzędzie narzędzia ITSM.
    
    d. **Wspólny schemat alertów**: można włączyć [wspólny schemat alertów](./alerts-common-schema.md), który umożliwia korzystanie z jednego rozszerzalnego i ujednoliconego ładunku alertów dla wszystkich usług alertów w Azure monitor.
    
    ![Karta akcje](./media/action-groups/action-group-3-actions.png)

### <a name="create-the-action-group"></a>Utwórz grupę akcji

1. Jeśli chcesz, możesz przejrzeć ustawienia **Tagi**. Pozwala to skojarzyć pary klucz/wartość z grupą akcji dla danej kategoryzacji i jest funkcją dostępną dla dowolnego zasobu platformy Azure.

    ![Karta Tagi](./media/action-groups/action-group-4-tags.png)
    
1. Kliknij przycisk **Przejrzyj i utwórz**, aby przejrzeć ustawienia. Spowoduje to szybkie sprawdzenie poprawności danych wejściowych, aby upewnić się, że są zaznaczone wszystkie wymagane pola. Jeśli występują problemy, zostaną zgłoszone w tym miejscu. Po przejrzeniu ustawień kliknij przycisk **Utwórz** , aby zainicjować obsługę administracyjną grupy akcji.
    
    ![Karta Recenzja + tworzenie](./media/action-groups/action-group-5-review.png)

> [!NOTE]
> Po skonfigurowaniu akcji powiadamiania osoby za pośrednictwem poczty e-mail lub wiadomości SMS otrzymujemy potwierdzenie wskazujące, że zostały dodane do grupy akcji.

## <a name="manage-your-action-groups"></a>Zarządzanie grupami akcji

Po utworzeniu grupy akcji można wyświetlić **grupy akcji** , wybierając pozycję **Zarządzaj akcjami** na stronie miejsce docelowe **alertów** w okienku **monitorowanie** . Wybierz grupę akcji, do której chcesz zarządzać:

* Dodawanie, edytowanie lub usuwanie akcji.
* Usuń grupę akcji.

## <a name="action-specific-information"></a>Informacje dotyczące akcji

> [!NOTE]
> Zobacz [limity usługi subskrypcji, aby monitorować](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-monitor-limits) limity liczbowe dla każdego z poniższych elementów.  

### <a name="automation-runbook"></a>Element runbook automatyzacji
Limity dotyczące ładunków elementów Runbook można znaleźć w [limitach usługi subskrypcji platformy Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md) .

W grupie akcji może istnieć ograniczona liczba akcji elementu Runbook. 

### <a name="azure-app-push-notifications"></a>Powiadomienia push aplikacji platformy Azure
Włącz powiadomienia wypychane do [aplikacja mobilna platformy Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/) , podając adres e-mail używany jako identyfikator konta podczas konfigurowania aplikacja mobilna platformy Azure.

W grupie akcji może istnieć ograniczona liczba akcji aplikacji platformy Azure.

### <a name="email"></a>E-mail
Wiadomości e-mail będą wysyłane z następujących adresów e-mail. Upewnij się, że filtrowanie poczty e-mail jest skonfigurowane odpowiednio
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

W grupie akcji może istnieć ograniczona liczba akcji poczty e-mail. Zobacz artykuł [Informacje o ograniczeniu szybkości](./alerts-rate-limiting.md) .

### <a name="email-azure-resource-manager-role"></a>Wyślij wiadomość e-mail do roli usługi Azure Resource Manager
Wyślij wiadomość e-mail do członków roli subskrypcji. Poczta e-mail będzie wysyłana tylko do członków roli **użytkownika usługi Azure AD** . Wiadomość e-mail nie zostanie wysłana do grup lub jednostek usługi Azure AD.

Wiadomość e-mail z powiadomieniem jest wysyłana tylko do *podstawowego adresu e-mail* .

Jeśli nie otrzymujesz powiadomień dotyczących *głównej poczty e-mail*, możesz spróbować wykonać następujące czynności:

1. W Azure Portal przejdź do *Active Directory*.
2. Kliknięcie przycisku wszyscy użytkownicy (w lewym okienku) spowoduje wyświetlenie listy użytkowników (w okienku po prawej stronie).
3. Wybierz użytkownika, dla którego chcesz przejrzeć podstawowe informacje o *wiadomości e-mail* .

  :::image type="content" source="media/action-groups/active-directory-user-profile.png" alt-text="Przykład dotyczący sposobu przeglądu profilu użytkownika." border="true":::

4. W obszarze profil użytkownika w obszarze informacje kontaktowe, jeśli karta "E-mail" jest pusta, kliknij przycisk *Edytuj* u góry i Dodaj swój *podstawowy adres e-mail* i naciśnij przycisk *Zapisz* w górnej części ekranu.

  :::image type="content" source="media/action-groups/active-directory-add-primary-email.png" alt-text="Przykład dodawania podstawowej poczty e-mail." border="true":::

W grupie akcji może istnieć ograniczona liczba akcji poczty e-mail. Zobacz artykuł [Informacje o ograniczeniu szybkości](./alerts-rate-limiting.md) .

### <a name="function"></a>Funkcja
Wywołuje istniejący punkt końcowy wyzwalacza HTTP w [Azure Functions](../../azure-functions/functions-get-started.md). Aby obsłużyć żądanie, punkt końcowy musi obsługiwać zlecenie HTTP POST.

W grupie akcji może istnieć ograniczona liczba akcji funkcji.

### <a name="itsm"></a>ITSM
Akcja narzędzia ITSM wymaga połączenia narzędzia ITSM. Dowiedz się, jak utworzyć [połączenie narzędzia ITSM](./itsmc-overview.md).

W grupie akcji może istnieć ograniczona liczba akcji narzędzia ITSM. 

### <a name="logic-app"></a>Aplikacja logiki
W grupie akcji może istnieć ograniczona liczba akcji aplikacji logiki.

### <a name="secure-webhook"></a>Bezpieczny element webhook

> [!NOTE]
> Użycie akcji elementu webhook wymaga, aby docelowy punkt końcowy elementu webhook nie wymagał pomyślnego funkcjonowania alertu lub może on przeanalizować informacje kontekstu alertu, które są dostarczane jako część operacji POST. Jeśli punkt końcowy elementu webhook nie może samodzielnie obsłużyć informacji kontekstu alertu, możesz użyć rozwiązania, takiego jak [Akcja aplikacji logiki](./action-groups-logic-app.md) do niestandardowego manipulowania informacjami kontekstu alertu w celu dopasowania do oczekiwanego formatu danych elementu webhook.
> Użytkownik powinien być **właścicielem** jednostki usługi elementu webhook, aby upewnić się, że zabezpieczenia nie zostały naruszone. Ponieważ dowolny klient platformy Azure może uzyskać dostęp do wszystkich identyfikatorów obiektów za pomocą portalu bez sprawdzania właściciela, każdy może dodać bezpieczny element webhook do własnej grupy akcji dla powiadomień o alertach usługi Azure monitor, które naruszają zabezpieczenia.

Akcja elementu webhook grup akcji umożliwia korzystanie z Azure Active Directory w celu zabezpieczenia połączenia między grupą akcji i chronionym internetowym interfejsem API (punkt końcowy elementu webhook). Poniżej opisano ogólny przepływ pracy w celu skorzystania z zalet tej funkcji. Omówienie aplikacji usługi Azure AD i nazw głównych usług można znaleźć w temacie [Microsoft Identity platform (v 2.0) — Omówienie](../../active-directory/develop/v2-overview.md).

1. Utwórz aplikację usługi Azure AD dla chronionego internetowego interfejsu API. Zobacz [chroniony internetowy interfejs API: Rejestracja aplikacji](../../active-directory/develop/scenario-protected-web-api-app-registration.md).
    - Skonfiguruj chroniony interfejs API do [wywoływania przez aplikację demona](../../active-directory/develop/scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app).
    
2. Włącz grupy akcji, aby korzystać z aplikacji usługi Azure AD.

    > [!NOTE]
    > Aby wykonać ten skrypt, musisz być członkiem [roli administratora aplikacji usługi Azure AD](../../active-directory/roles/permissions-reference.md#all-roles) .
    
    - Zmodyfikuj wywołanie Connect-AzureAD skryptu programu PowerShell, aby użyć identyfikatora dzierżawy usługi Azure AD.
    - Zmodyfikuj zmienną $myAzureADApplicationObjectId skryptu programu PowerShell, aby użyć identyfikatora obiektu aplikacji usługi Azure AD.
    - Uruchom zmodyfikowany skrypt.
    
3. Skonfiguruj akcję bezpiecznego elementu webhook grupy akcji.
    - Skopiuj wartość $myApp. ObjectId ze skryptu i wprowadź ją w polu Identyfikator obiektu aplikacji w definicji akcji elementu webhook.
    
    ![Akcja bezpiecznego elementu webhook](./media/action-groups/action-groups-secure-webhook.png)

#### <a name="secure-webhook-powershell-script"></a>Bezpieczny skrypt programu PowerShell elementu webhook

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
Aby uzyskać dodatkowe informacje, zobacz [informacje dotyczące ograniczania szybkości](./alerts-rate-limiting.md) oraz [zachowanie alertu programu SMS](./alerts-sms-behavior.md) . 

W grupie akcji może istnieć ograniczona liczba akcji programu SMS.

> [!NOTE]
> Jeśli interfejs użytkownika grupy akcji Azure Portal nie pozwala na wybranie kodu kraju/regionu, to wiadomość SMS nie jest obsługiwana w Twoim kraju/regionie.  Jeśli kod kraju/regionu nie jest dostępny, możesz głosować na to, aby Twój kraj/region został dodany przy [głosowaniu użytkownika](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice). W międzyczasie obejście problemu polega na tym, że grupa akcji wywoła element webhook do dostawcy programu SMS innej firmy z obsługą w Twoim kraju/regionie.  

Cennik dla obsługiwanych krajów/regionów znajduje się na [stronie cennika Azure monitor](https://azure.microsoft.com/pricing/details/monitor/).

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
Zapoznaj się z artykułem [dotyczącym ograniczania szybkości](./alerts-rate-limiting.md) , aby uzyskać dodatkowe ważne zachowanie.

W grupie akcji może istnieć ograniczona liczba akcji głosowych.

> [!NOTE]
> Jeśli interfejs użytkownika grupy akcji Azure Portal nie pozwala na wybranie kodu kraju/regionu, połączenia głosowe nie są obsługiwane w Twoim kraju/regionie. Jeśli kod kraju/regionu nie jest dostępny, możesz głosować na to, aby Twój kraj/region został dodany przy [głosowaniu użytkownika](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice).  Tymczasem obejście jest to, że grupa akcji wywoła element webhook do dostawcy wywołań głosowych innej firmy z obsługą w Twoim kraju/regionie.  
> Tylko kod kraju obsługiwany obecnie w Azure Portal grupie akcji dla powiadomienia głosowego to + 1 (Stany Zjednoczone). 

Cennik dla obsługiwanych krajów/regionów znajduje się na [stronie cennika Azure monitor](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="webhook"></a>Webhook

> [!NOTE]
> Użycie akcji elementu webhook wymaga, aby docelowy punkt końcowy elementu webhook nie wymagał pomyślnego funkcjonowania alertu lub może on przeanalizować informacje kontekstu alertu, które są dostarczane jako część operacji POST. Jeśli punkt końcowy elementu webhook nie może samodzielnie obsłużyć informacji kontekstu alertu, możesz użyć rozwiązania, takiego jak [Akcja aplikacji logiki](./action-groups-logic-app.md) do niestandardowego manipulowania informacjami kontekstu alertu w celu dopasowania do oczekiwanego formatu danych elementu webhook.

Elementy webhook są przetwarzane przy użyciu następujących reguł
- Podjęto próbę wywołania elementu webhook z maksymalnie 3 razy.
- Wywołanie zostanie ponowione, jeśli odpowiedź nie zostanie odebrana w określonym limicie czasu lub zostanie zwrócony jeden z następujących kodów stanu HTTP: 408, 429, 503 lub 504.
- Pierwsze wywołanie będzie oczekiwać 10 sekund na odpowiedź.
- Druga i trzecia próba odczeka 30 sekund na odpowiedź.
- Gdy 3 próby wywołania elementu webhook zakończą się niepowodzeniem, żadna grupa akcji nie zadzwoni punktu końcowego przez 15 minut.

Zobacz [adresy IP grupy akcji](../app/ip-addresses.md) dla zakresów źródłowych adresów IP.


## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej na temat [zachowania alertu programu SMS](./alerts-sms-behavior.md).  
* Uzyskaj [informacje na temat schematu elementu webhook alertu dziennika aktywności](./activity-log-alerts-webhook.md).  
* Dowiedz się więcej o [Łącznik ITSM](./itsmc-overview.md).
* Dowiedz się więcej o [ograniczaniu](./alerts-rate-limiting.md) liczby alertów.
* Zapoznaj się z [omówieniem alertów dziennika aktywności](./alerts-overview.md)i Dowiedz się, jak otrzymywać alerty.  
* Informacje o sposobie [konfigurowania alertów za każdym razem, gdy jest ogłaszane powiadomienie o kondycji usługi](../../service-health/alerts-activity-log-service-notifications-portal.md).
