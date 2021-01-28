---
title: Ustawianie okresów istnienia tokenów
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak ustawiać okresy istnienia tokenów wystawionych przez platformę tożsamości firmy Microsoft. Dowiedz się, jak zarządzać zasadami domyślnymi organizacji, utworzyć zasady logowania do sieci Web, utworzyć zasady dla aplikacji natywnej, która wywołuje interfejs API sieci Web, i zarządzać zaawansowanymi zasadami.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 01/04/2021
ms.author: ryanwi
ms.custom: aaddev, content-perf, FY21Q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: 19330af5e4c0e4962993d0ed89ec9bcd4a50514a
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98986417"
---
# <a name="configure-token-lifetime-policies-preview"></a>Konfigurowanie zasad okresu istnienia tokenu (wersja zapoznawcza)
Można określić okres istnienia tokenu dostępu, SAML lub identyfikatora wystawionego przez platformę tożsamości firmy Microsoft. Okresy istnienia tokenów można ustawić dla wszystkich aplikacji w organizacji, dla aplikacji wielodostępnych (dla wielu organizacji) lub dla określonej jednostki usługi w organizacji. Aby uzyskać więcej informacji, Przeczytaj [konfigurowalne okresy istnienia tokenu](active-directory-configurable-token-lifetimes.md).

W tej sekcji omówiono typowe scenariusze dotyczące zasad, które mogą pomóc w wprowadzeniu nowych reguł dla okresu istnienia tokenu. W tym przykładzie dowiesz się, jak utworzyć zasady, które wymagają, aby użytkownicy uwierzytelniali się częściej w aplikacji sieci Web.

## <a name="get-started"></a>Rozpoczęcie pracy
Aby rozpocząć, wykonaj następujące czynności:

1. Pobierz najnowszą [wersję publicznej wersji zapoznawczej modułu programu Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).
1. Uruchom `Connect` polecenie, aby zalogować się do konta administratora usługi Azure AD. Uruchom to polecenie przy każdym uruchomieniu nowej sesji.

    ```powershell
    Connect-AzureAD -Confirm
    ```

1. Aby wyświetlić wszystkie zasady, które zostały utworzone w organizacji, uruchom polecenie cmdlet [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) .  Wszystkie wyniki ze zdefiniowanymi wartościami właściwości, które różnią się od wartości domyślnych wymienionych powyżej, znajdują się w zakresie wycofania.

    ```powershell
    Get-AzureADPolicy -All $true
    ```

1. Aby sprawdzić, które aplikacje i jednostki usługi są połączone z konkretnymi określonymi zasadami, należy uruchomić następujące polecenie cmdlet [Get-AzureADPolicyAppliedObject](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) , zastępując **1a37dad8-5da7-4cc8-87c7-efbc0326cf20** identyfikatorem zasad. Następnie możesz zdecydować, czy skonfigurować częstotliwość logowania dostępu warunkowego, czy pozostawać z ustawieniami domyślnymi usługi Azure AD.

    ```powershell
    Get-AzureADPolicyAppliedObject -id 1a37dad8-5da7-4cc8-87c7-efbc0326cf20
    ```

Jeśli dzierżawa zawiera zasady, które definiują wartości niestandardowe właściwości konfiguracji odświeżania i tokenu sesji, firma Microsoft zaleca aktualizację tych zasad do wartości, które odzwierciedlają ustawienia domyślne opisane powyżej. Jeśli nie zostaną wprowadzone żadne zmiany, usługa Azure AD będzie automatycznie przestrzegać wartości domyślnych.

## <a name="create-a-policy-for-web-sign-in"></a>Tworzenie zasad logowania do sieci Web

W tym przykładzie utworzysz zasady, które wymagają, aby użytkownicy uwierzytelniali się częściej w aplikacji sieci Web. Te zasady określają okres istnienia tokenów dostępu/identyfikatora oraz maksymalny wiek tokenu sesji wieloskładnikowej do jednostki usługi aplikacji sieci Web.

1. Utwórz zasady czasu istnienia tokenu.

    Te zasady dla logowania za pomocą sieci Web określają okres istnienia tokenu dostępu/identyfikatora oraz maksymalny wiek tokenu sesji pojedynczego czynnika na dwie godziny.

    1. Aby utworzyć zasady, uruchom polecenie cmdlet [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. Aby wyświetlić nowe zasady i uzyskać identyfikator **objectid** zasad, uruchom polecenie cmdlet [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Przypisz zasady do nazwy głównej usługi. Należy również uzyskać identyfikator **objectid** nazwy głównej usługi.

    1. Użyj polecenia cmdlet [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) , aby wyświetlić wszystkie nazwy główne usługi lub pojedynczą jednostkę usługi.
        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
        ```

    1. Jeśli masz nazwę główną usługi, uruchom polecenie cmdlet [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) :
        ```powershell
        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

## <a name="create-token-lifetime-policies-for-refresh-and-session-tokens"></a>Tworzenie zasad czasu istnienia tokenu dla odświeżania i tokenów sesji
> [!IMPORTANT]
> Od maja 2020 nowi dzierżawcy nie mogą konfigurować okresów istnienia odświeżania i tokenu sesji.  Dzierżawy z istniejącą konfiguracją mogą modyfikować zasady odświeżania i tokenów sesji do 30 stycznia 2021.  Azure Active Directory przestanie przestrzegać istniejących konfiguracji odświeżania i tokenu sesji w zasadach po 30 stycznia 2021. Po wycofaniu można nadal konfigurować okresy istnienia tokenu dostępu, SAML i identyfikatora.
>
> Jeśli musisz nadal zdefiniować okres, po upływie którego użytkownik zostanie poproszony o ponowne zalogowanie, skonfiguruj częstotliwość logowania w polu dostęp warunkowy. Aby dowiedzieć się więcej na temat dostępu warunkowego, przeczytaj artykuł [Konfigurowanie sesji uwierzytelniania zarządzanie przy użyciu dostępu warunkowego](../conditional-access/howto-conditional-access-session-lifetime.md).
>
> Jeśli nie chcesz korzystać z dostępu warunkowego po dacie wycofania, tokeny odświeżania i sesji zostaną ustawione na [domyślną konfigurację](active-directory-configurable-token-lifetimes.md#configurable-token-lifetime-properties-after-the-retirement) w tej dacie i nie będzie już można zmienić ich okresów istnienia.

### <a name="manage-an-organizations-default-policy"></a>Zarządzanie zasadami domyślnymi organizacji
W tym przykładzie utworzysz zasady, które umożliwiają logowanie użytkowników rzadziej w całej organizacji. W tym celu należy utworzyć zasady istnienia tokenu dla tokenów odświeżania z jednym czynnikiem, które są stosowane w całej organizacji. Zasady są stosowane do każdej aplikacji w organizacji oraz do każdej jednostki usługi, która nie ma jeszcze zestawu zasad.

1. Utwórz zasady czasu istnienia tokenu.

    1. Ustaw token jednoskładnikowego odświeżania na "do odwołania". Token nie wygasa, dopóki dostęp nie zostanie odwołany. Utwórz następującą definicję zasad:

        ```powershell
        @('{
            "TokenLifetimePolicy":
            {
                "Version":1,
                "MaxAgeSingleFactor":"until-revoked"
            }
        }')
        ```

    1. Aby utworzyć zasady, uruchom polecenie cmdlet [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. Aby usunąć wszystkie odstępy, uruchom polecenie cmdlet [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        Get-AzureADPolicy -id | set-azureadpolicy -Definition @($((Get-AzureADPolicy -id ).Replace(" ","")))
        ```

    1. Aby wyświetlić nowe zasady i uzyskać identyfikator **objectid** zasad, uruchom następujące polecenie:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Zaktualizuj zasady.

    Możesz zdecydować, że pierwsze zasady ustawione w tym przykładzie nie są zgodne z wymaganiami usługi. Aby ustawić token odświeżania jednoskładnikowego wygaśnie w ciągu dwóch dni, uruchom następujące polecenie:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName $policy.DisplayName -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

### <a name="create-a-policy-for-a-native-app-that-calls-a-web-api"></a>Tworzenie zasad dla aplikacji natywnej, która wywołuje interfejs API sieci Web
W tym przykładzie utworzysz zasady, które wymagają, aby użytkownicy byli uwierzytelniani rzadziej. Zasada wydłuża również czas nieaktywności użytkownika, po upływie którego użytkownik musi ponownie przeprowadzić uwierzytelnienie. Zasady są stosowane do internetowego interfejsu API. Gdy aplikacja natywna zażąda internetowego interfejsu API jako zasobu, te zasady są stosowane.

1. Utwórz zasady czasu istnienia tokenu.

    1. Aby utworzyć ścisłe zasady dla internetowego interfejsu API, uruchom polecenie cmdlet [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. Aby wyświetlić nowe zasady, uruchom następujące polecenie:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Przypisz zasady do internetowego interfejsu API. Należy również uzyskać identyfikator **objectid** aplikacji. Użyj polecenia cmdlet [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) , aby znaleźć **Identyfikator obiektu aplikacji**, lub Użyj [Azure Portal](https://portal.azure.com/).

    Pobierz identyfikator **objectid** aplikacji i przypisz zasady:

    ```powershell
    # Get the application
    $app = Get-AzureADApplication -Filter "DisplayName eq 'Fourth Coffee Web API'"

    # Assign the policy to your web API.
    Add-AzureADApplicationPolicy -Id $app.ObjectId -RefObjectId $policy.Id
    ```

### <a name="manage-an-advanced-policy"></a>Zarządzanie zaawansowanymi zasadami
W tym przykładzie utworzysz kilka zasad, aby dowiedzieć się, jak działa system priorytetów. Dowiesz się również, jak zarządzać wieloma zasadami, które są stosowane do kilku obiektów.

1. Utwórz zasady czasu istnienia tokenu.

    1. Aby utworzyć zasady domyślne dla organizacji, które określają okres istnienia tokenu odświeżania pojedynczego czynnika na 30 dni, uruchom polecenie cmdlet [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. Aby wyświetlić nowe zasady, uruchom polecenie cmdlet [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Przypisz zasady do jednostki usługi.

    Teraz masz zasady, które mają zastosowanie do całej organizacji. Możesz chcieć zachować te 30-dniowe zasady dla określonej nazwy głównej usługi, ale zmienić domyślne zasady organizacji na górny limit "do odwołania".

    1. Aby wyświetlić wszystkie nazwy główne usługi w organizacji, należy użyć polecenia cmdlet [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) .

    1. Jeśli masz nazwę główną usługi, uruchom polecenie cmdlet [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

1. Ustaw `IsOrganizationDefault` flagę na wartość false:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
    ```

1. Utwórz nową zasadę domyślną dla organizacji:

    ```powershell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    Masz już oryginalne zasady połączone z jednostką usługi, a nowe zasady są ustawione jako domyślne zasady organizacji. Należy pamiętać, że zasady stosowane do jednostek usługi mają priorytet w porównaniu z zasadami domyślnymi organizacji.

## <a name="next-steps"></a>Następne kroki
Informacje o [możliwościach zarządzania sesjami uwierzytelniania](../conditional-access/howto-conditional-access-session-lifetime.md) w usłudze Azure AD dostęp warunkowy.
