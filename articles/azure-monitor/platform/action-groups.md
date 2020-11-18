---
title: Tworzenie grup akcji i zarządzanie nimi w Azure Portal
description: Dowiedz się, jak tworzyć grupy akcji i zarządzać nimi w Azure Portal.
author: dkamstra
ms.topic: conceptual
ms.date: 07/28/2020
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: a5d685e49d941d7b6febbc220cdbfbcb631c4496
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94746367"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Tworzenie grup akcji i zarządzanie nimi w Azure Portal
Grupa akcji to zbiór preferencji powiadomień definiowanych przez właściciela subskrypcji platformy Azure. Alerty Azure Monitor i Service Health umożliwiają Powiadamianie użytkowników o wyzwoleniu alertu. Różne alerty mogą korzystać z tej samej grupy akcji lub różnych grup akcji w zależności od wymagań użytkownika. W ramach subskrypcji można skonfigurować maksymalnie 2 000 grup akcji.

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

W grupie akcji może istnieć ograniczona liczba akcji poczty e-mail. Zobacz artykuł [Informacje o ograniczeniu szybkości](./alerts-rate-limiting.md) .

### <a name="function"></a>Funkcja
Wywołuje istniejący punkt końcowy wyzwalacza HTTP w [Azure Functions](../../azure-functions/functions-create-first-azure-function.md#create-a-function-app).

W grupie akcji może istnieć ograniczona liczba akcji funkcji.

### <a name="itsm"></a>ITSM
Akcja narzędzia ITSM wymaga połączenia narzędzia ITSM. Dowiedz się, jak utworzyć [połączenie narzędzia ITSM](./itsmc-overview.md).

W grupie akcji może istnieć ograniczona liczba akcji narzędzia ITSM. 

### <a name="logic-app"></a>Aplikacja logiki
W grupie akcji może istnieć ograniczona liczba akcji aplikacji logiki.

### <a name="secure-webhook"></a>Bezpieczny element webhook
Akcja elementu webhook grup akcji umożliwia korzystanie z Azure Active Directory w celu zabezpieczenia połączenia między grupą akcji i chronionym internetowym interfejsem API (punkt końcowy elementu webhook). Poniżej opisano ogólny przepływ pracy w celu skorzystania z zalet tej funkcji. Omówienie aplikacji usługi Azure AD i nazw głównych usług można znaleźć w temacie [Microsoft Identity platform (v 2.0) — Omówienie](../../active-directory/develop/v2-overview.md).

1. Utwórz aplikację usługi Azure AD dla chronionego internetowego interfejsu API. Zobacz [chroniony internetowy interfejs API: Rejestracja aplikacji](../../active-directory/develop/scenario-protected-web-api-app-registration.md).
    - Skonfiguruj chroniony interfejs API do [wywoływania przez aplikację demona](../../active-directory/develop/scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app).
    
2. Włącz grupy akcji, aby korzystać z aplikacji usługi Azure AD.

    > [!NOTE]
    > Aby wykonać ten skrypt, musisz być członkiem [roli administratora aplikacji usługi Azure AD](../../active-directory/roles/permissions-reference.md#available-roles) .
    
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
Aby uzyskać dodatkowe informacje, zobacz [informacje dotyczące ograniczania szybkości](./alerts-rate-limiting.md) oraz [zachowanie alertu programu SMS](./alerts-sms-behavior.md) . 

W grupie akcji może istnieć ograniczona liczba akcji programu SMS.

> [!NOTE]
> Jeśli interfejs użytkownika grupy akcji Azure Portal nie pozwala na wybranie kodu kraju/regionu, to wiadomość SMS nie jest obsługiwana w Twoim kraju/regionie.  Jeśli kod kraju/regionu nie jest dostępny, możesz głosować na to, aby Twój kraj/region został dodany przy [głosowaniu użytkownika](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice). W międzyczasie obejście problemu polega na tym, że grupa akcji wywoła element webhook do dostawcy programu SMS innej firmy z obsługą w Twoim kraju/regionie.  

Cennik dla obsługiwanych krajów/regionów znajduje się na [stronie cennika Azure monitor](https://azure.microsoft.com/pricing/details/monitor/).
  

### <a name="voice"></a>Połączenia głosowe
Zapoznaj się z artykułem [dotyczącym ograniczania szybkości](./alerts-rate-limiting.md) , aby uzyskać dodatkowe ważne zachowanie.

W grupie akcji może istnieć ograniczona liczba akcji głosowych.

> [!NOTE]
> Jeśli interfejs użytkownika grupy akcji Azure Portal nie pozwala na wybranie kodu kraju/regionu, połączenia głosowe nie są obsługiwane w Twoim kraju/regionie. Jeśli kod kraju/regionu nie jest dostępny, możesz głosować na to, aby Twój kraj/region został dodany przy [głosowaniu użytkownika](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice).  Tymczasem obejście jest to, że grupa akcji wywoła element webhook do dostawcy wywołań głosowych innej firmy z obsługą w Twoim kraju/regionie.  

Cennik dla obsługiwanych krajów/regionów znajduje się na [stronie cennika Azure monitor](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="webhook"></a>Webhook
Elementy webhook są przetwarzane przy użyciu następujących reguł
- Podjęto próbę wywołania elementu webhook z maksymalnie 3 razy.
- Wywołanie zostanie ponowione, jeśli odpowiedź nie zostanie odebrana w określonym limicie czasu lub zostanie zwrócony jeden z następujących kodów stanu HTTP: 408, 429, 503 lub 504.
- Pierwsze wywołanie będzie oczekiwać 10 sekund na odpowiedź.
- Druga i trzecia próba odczeka 30 sekund na odpowiedź.
- Gdy 3 próby wywołania elementu webhook zakończą się niepowodzeniem, żadna grupa akcji nie zadzwoni punktu końcowego przez 15 minut.

Zakresy źródłowych adresów IP:

 - 13.66.60.119/32
 - 13.66.143.220/30
 - 13.66.202.14/32
 - 13.66.248.225/32
 - 13.66.249.211/32
 - 13.67.10.124/30
 - 13.69.109.132/30
 - 13.71.199.112/30
 - 13.77.53.216/30
 - 13.77.172.102/32
 - 13.77.183.209/32
 - 13.78.109.156/30
 - 13.84.49.247/32
 - 13.84.51.172/32
 - 13.84.52.58/32
 - 13.86.221.220/30
 - 13.106.38.142/32
 - 13.106.38.148/32
 - 13.106.54.3/32
 - 13.106.54.19/32
 - 13.106.57.181/32
 - 13.106.57.196/31
 - 20.38.149.132/30
 - 20.42.64.36/30
 - 20.43.121.124/30
 - 20.44.17.220/30
 - 20.45.123.236/30
 - 20.72.27.152/30
 - 20.150.172.228/30
 - 20.192.238.124/30
 - 20.193.202.4/30
 - 40.68.195.137/32
 - 40.68.201.58/32
 - 40.68.201.65/32
 - 40.68.201.206/32
 - 40.68.201.211/32
 - 40.68.204.18/32
 - 40.115.37.106/32
 - 40.121.219.215/32
 - 40.121.221.62/32
 - 40.121.222.201/32
 - 40.121.223.186/32
 - 51.104.9.100/30
 - 52.183.20.244/32
 - 52.183.31.0/32
 - 52.183.94.59/32
 - 52.184.145.166/32
 - 191.233.50.4/30
 - 191.233.207.64/26
 - 2603:1000:4:402::178/125
 - 2603:1000:104:402::178/125
 - 2603:1010:6:402::178/125
 - 2603:1010:101:402::178/125
 - 2603:1010:304:402::178/125
 - 2603:1010:404:402::178/125
 - 2603:1020:5:402::178/125
 - 2603:1020:206:402::178/125
 - 2603:1020:305:402::178/125
 - 2603:1020:405:402::178/125
 - 2603:1020:605:402::178/125
 - 2603:1020:705:402::178/125
 - 2603:1020:805:402::178/125
 - 2603:1020:905:402::178/125
 - 2603:1020: A04:402:: 178/125
 - 2603:1020: B04:402:: 178/125
 - 2603:1020: C04:402:: 178/125
 - 2603:1020: D04:402:: 178/125
 - 2603:1020: E04:402:: 178/125
 - 2603:1020: F04:402:: 178/125
 - 2603:1020:1004:800:: F8/125
 - 2603:1020:1104:400::178/125
 - 2603:1030: f:400:: 978/125
 - 2603:1030:10:402::178/125
 - 2603:1030:104:402::178/125
 - 2603:1030:107:400:: F0/125
 - 2603:1030:210:402::178/125
 - 2603:1030:40B: 400:: 978/125
 - 2603:1030:40C: 402:: 178/125
 - 2603:1030:504:802:: F8/125
 - 2603:1030:608:402::178/125
 - 2603:1030:807:402::178/125
 - 2603:1030: A07:402:: 8f8/125
 - 2603:1030: B04:402:: 178/125
 - 2603:1030: C06:400:: 978/125
 - 2603:1030: F05:402:: 178/125
 - 2603:1030:1005:402::178/125
 - 2603:1040:5:402::178/125
 - 2603:1040:207:402::178/125
 - 2603:1040:407:402::178/125
 - 2603:1040:606:402::178/125
 - 2603:1040:806:402::178/125
 - 2603:1040:904:402::178/125
 - 2603:1040: A06:402:: 178/125
 - 2603:1040: B04:402:: 178/125
 - 2603:1040: C06:402:: 178/125
 - 2603:1040: D04:800:: F8/125
 - 2603:1040: F05:402:: 178/125
 - 2603:1040:1104:400::178/125
 - 2603:1050:6:402::178/125
 - 2603:1050:403:400:: 1F8/125

Aby otrzymywać aktualizacje dotyczące zmian wprowadzonych w tych adresach IP, zalecamy skonfigurowanie alertu Service Health, który monitoruje powiadomienia informacyjne o usłudze grup akcji.

W grupie akcji może istnieć ograniczona liczba akcji elementu webhook.

Częste aktualizacje źródłowych adresów IP mogą być dość czasochłonne w elemencie webhook. Korzystanie z **tagu usługi** dla obiektu *Actions* ułatwia zminimalizowanie złożoności często używanych ręcznie aktualizacji adresów IP. Prefiksy zakresów źródłowych adresów IP udostępnione powyżej są zarządzane przez firmę Microsoft, co obejmuje **tag usługi**.

#### <a name="service-tag"></a>Tag usługi
Tag usługi reprezentuje grupę prefiksów adresów IP z danej usługi platformy Azure. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy, minimalizując złożoność częstych aktualizacji reguł zabezpieczeń sieci dla obiektu ActionType.

1. W Azure Portal w obszarze usługi platformy Azure Wyszukaj *grupę zabezpieczeń sieci*.
2. Kliknij pozycję **Dodaj** i Utwórz sieciową grupę zabezpieczeń.

   1. Dodaj nazwę grupy zasobów, a następnie wprowadź *szczegóły wystąpienia*.
   1. Kliknij przycisk **Przegląd + Utwórz** , a następnie kliknij przycisk *Utwórz*.
   
   :::image type="content" source="media/action-groups/action-group-create-security-group.png" alt-text="Przykład tworzenia sieciowej grupy zabezpieczeń."border="true":::

3. Przejdź do grupy zasobów, a następnie kliknij utworzoną *grupę zabezpieczeń sieci* .

    1. Wybierz pozycję *reguły zabezpieczeń dla ruchu przychodzącego*.
    1. Kliknij przycisk **Dodaj**.
    
    :::image type="content" source="media/action-groups/action-group-add-service-tag.png" alt-text="Przykład dodawania znacznika usługi."border="true":::

4. Nowe okno zostanie otwarte w okienku po prawej stronie.
    1.  Wybierz źródło: **tag usługi**
    1.  Tag usługi źródłowej: **ActionName**
    1.  Kliknij pozycję **Dodaj**.
    
    :::image type="content" source="media/action-groups/action-group-service-tag.png" alt-text="Przykład dotyczący dodawania tagu usługi."border="true":::

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej na temat [zachowania alertu programu SMS](./alerts-sms-behavior.md).  
* Uzyskaj [informacje na temat schematu elementu webhook alertu dziennika aktywności](./activity-log-alerts-webhook.md).  
* Dowiedz się więcej o [Łącznik ITSM](./itsmc-overview.md).
* Dowiedz się więcej o [ograniczaniu](./alerts-rate-limiting.md) liczby alertów.
* Zapoznaj się z [omówieniem alertów dziennika aktywności](./alerts-overview.md)i Dowiedz się, jak otrzymywać alerty.  
* Informacje o sposobie [konfigurowania alertów za każdym razem, gdy jest ogłaszane powiadomienie o kondycji usługi](../../service-health/alerts-activity-log-service-notifications-portal.md).
