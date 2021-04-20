---
title: Portal deweloperów — często zadawane pytania
titleSuffix: Azure API Management
description: Często zadawane pytania dotyczące portalu dla deweloperów w witrynie API Management. Portal dla deweloperów to dostosowywalna witryna internetowa, w której użytkownicy interfejsu API mogą eksplorować Twoje interfejsy API.
services: api-management
documentationcenter: API Management
author: mikebudzynski
ms.service: api-management
ms.topic: article
ms.date: 04/15/2021
ms.author: apimpm
ms.openlocfilehash: b4413bc53cf5c86c311d049046db790582737ca4
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741832"
---
# <a name="api-management-developer-portal---frequently-asked-questions"></a>API Management dla deweloperów — często zadawane pytania

## <a name="what-if-i-need-functionality-that-isnt-supported-in-the-portal"></a>Co zrobić, jeśli potrzebuję funkcji, które nie są obsługiwane w portalu?

Możesz otworzyć żądanie funkcji w repozytorium [GitHub](https://github.com/Azure/api-management-developer-portal) lub [samodzielnie zaimplementować brakujące funkcje.](developer-portal-implement-widgets.md) Dowiedz się więcej o [rozszerzalności portalu dla deweloperów.](api-management-howto-developer-portal.md#managed-vs-self-hosted)


## <a name="can-i-have-multiple-developer-portals-in-one-api-management-service"></a>Czy mogę mieć wiele portali dla deweloperów w jednej API Management usługi?

Możesz mieć jeden zarządzany portal i wiele portali samodzielnie hostowanych. Zawartość wszystkich portali jest przechowywana w tej samej usłudze API Management, więc będą identyczne. Jeśli chcesz rozróżnić wygląd i funkcjonalność portali, możesz hostować je samodzielnie przy użyciu własnych niestandardowych widżetów, które dynamicznie dostosowywują strony w środowisku uruchomieniowym, na przykład na podstawie adresu URL.

## <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>Czy portal obsługuje Azure Resource Manager i/lub jest zgodny z zestawem API Management DevOps Resource Kit?

Nie.

## <a name="is-the-portals-content-saved-with-the-backuprestore-functionality-in-api-management"></a>Czy zawartość portalu jest zapisywana z funkcjami tworzenia/przywracania kopii zapasowych w API Management?

Nie.

## <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>Czy muszę włączyć dodatkową łączność z siecią wirtualną dla zależności portalu zarządzanego?

W większości przypadków — nie.

Jeśli Twoja API Management znajduje się w wewnętrznej sieci wirtualnej, portal deweloperów jest dostępny tylko z poziomu sieci. Nazwa hosta punktu końcowego zarządzania musi zostać rozpoznana jako wewnętrzny adres VIP usługi z maszyny, za pomocą których uzyskujesz dostęp do interfejsu administracyjnego portalu. Upewnij się, że punkt końcowy zarządzania jest zarejestrowany w systemie DNS. W przypadku nieprawidłowej konfiguracji zostanie wyświetlony błąd: `Unable to start the portal. See if settings are specified correctly in the configuration (...)` .

Jeśli Twoja usługa API Management znajduje się w wewnętrznej sieci wirtualnej i uzyskujesz do nich dostęp za pośrednictwem sieci Application Gateway z Internetu, upewnij się, że włączyć łączność z portalem dla deweloperów i punktami końcowymi zarządzania usługi API Management. Może być konieczne wyłączenie Web Application Firewall reguł. Aby [uzyskać więcej informacji, zobacz](api-management-howto-integrate-internal-vnet-appgateway.md) ten artykuł dokumentacji.

## <a name="i-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>Przypisano niestandardową domenę API Management, a opublikowany portal nie działa

Po zaktualizowaniu domeny należy [ponownie opublikować portal, aby](api-management-howto-developer-portal-customize.md#publish) zmiany zostały wprowadzone.

## <a name="i-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>Dodano dostawcę tożsamości i nie widzę go w portalu

Po skonfigurowaniu dostawcy tożsamości (na przykład usługi Azure AD, Azure AD B2C) należy ponownie opublikować [portal,](api-management-howto-developer-portal-customize.md#publish) aby zmiany zostały wprowadzone. Upewnij się, że strony portalu dla deweloperów zawierają widżet przycisków OAuth.

## <a name="i-set-up-delegation-and-the-portal-doesnt-use-it"></a>Po skonfigurowaniu delegowania portal go nie używa

Po skonfigurowaniu delegowania należy [ponownie opublikować portal,](api-management-howto-developer-portal-customize.md#publish) aby zmiany zostały wprowadzone.

## <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>Moje inne API Management konfiguracji nie zostały propagowane w portalu dla deweloperów

Większość zmian konfiguracji (na przykład sieć wirtualna, logowanie, warunki produktu) wymaga ponownego [opublikowania portalu.](api-management-howto-developer-portal-customize.md#publish)

## <a name="im-getting-a-cors-error-when-using-the-interactive-console"></a><a name="cors"></a> Podczas korzystania z konsoli interakcyjnej występuje błąd CORS

Interaktywna konsola wysyła żądanie interfejsu API po stronie klienta z przeglądarki. Rozwiąż problem z corsem, [dodając zasady CORS](api-management-cross-domain-policies.md#CORS) do interfejsów API.

Stan zasad CORS można sprawdzić w sekcji **Przeglądu** portalu usługi API Management w witrynie Azure Portal. Pole ostrzeżenia wskazuje brak lub błędnie skonfigurowane zasady.

> [!NOTE]
> 
> Wykonywana jest tylko jedna zasada CORS. Jeśli określono wiele zasad CORS (na przykład na poziomie interfejsu API i na poziomie wszystkich interfejsów API), interaktywna konsola może nie działać zgodnie z oczekiwaniami.

![Zrzut ekranu przedstawiający, gdzie można sprawdzić stan zasad CORS.](media/developer-portal-faq/cors-azure-portal.png)

Automatycznie zastosuj zasady CORS, klikając przycisk **Włącz cors.**

Można również włączyć funkcję CORS ręcznie.

1. Wybierz link **Ręcznie zastosuj go na poziomie globalnym,** aby wyświetlić wygenerowany kod zasad.
2. Przejdź do **sekcji Interfejsy** API w sekcji **Interfejsy** API usługi API Management w Azure Portal.
3. Wybierz **</>** ikonę w sekcji **Przychodzące przetwarzanie.**
4. Wstaw zasady w **<inbound>** sekcji pliku XML. Upewnij **<origin>** się, że wartość jest odpowiada domenie portalu deweloperów.

> [!NOTE]
> 
> Jeśli zasady CORS są stosowane w zakresie produktu, a nie w zakresie interfejsów API, a interfejs API używa uwierzytelniania klucza subskrypcji za pośrednictwem nagłówka, konsola nie będzie działać.
>
> Przeglądarka automatycznie wysyła `OPTIONS` żądanie HTTP, które nie zawiera nagłówka z kluczem subskrypcji. Z powodu braku klucza subskrypcji API Management nie może skojarzyć wywołania z produktem, dlatego nie może zastosować `OPTIONS` zasad CORS.
>
> Aby obejść ten problem, można przekazać klucz subskrypcji w parametrze zapytania.

## <a name="what-is-the-cors-proxy-feature-and-when-should-i-use-it"></a>Jaka jest funkcja serwera proxy CORS i kiedy należy jej używać?

Wybierz opcję Użyj serwera **proxy CORS** w konfiguracji widżetu szczegółów operacji interfejsu API, aby przekierowyć wywołania interfejsu API konsoli interakcyjnej za pośrednictwem zaplecza portalu w usłudze API Management wirtualnej. W tej konfiguracji nie trzeba już stosować zasad CORS dla interfejsów API, a łączność z punktem końcowym bramy z komputera lokalnego nie jest wymagana. Jeśli interfejsy API są udostępniane za pośrednictwem własnej bramy lub usługa znajduje się w sieci wirtualnej, wymagana jest łączność między usługą zaplecza API Management a bramą. Jeśli używasz własnego portalu, określ punkt końcowy zaplecza portalu przy użyciu `backendUrl` opcji w plikach konfiguracji. W przeciwnym razie samodzielnie hostowany portal nie będzie świadomy lokalizacji usługi zaplecza.

## <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>Jakie uprawnienia są potrzebne do edytowania portalu dla deweloperów?

Jeśli widzisz błąd podczas otwierania portalu w trybie administracyjnym, może brakować wymaganych uprawnień `Oops. Something went wrong. Please try again later.` (RBAC platformy Azure).

Starsze portale wymagały uprawnień w zakresie usługi ( ), aby zezwolić administratorowi użytkowników na dostęp `Microsoft.ApiManagement/service/getssotoken/action` `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>` do portali. Nowy portal wymaga uprawnienia `Microsoft.ApiManagement/service/users/token/action` w zakresie `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1` .

Aby utworzyć rolę z wymaganymi uprawnieniami, możesz użyć następującego skryptu programu PowerShell. Pamiętaj, aby zmienić `<subscription-id>` parametr . 

```powershell
#New Portals Admin Role 
Import-Module Az 
Connect-AzAccount 
$contributorRole = Get-AzRoleDefinition "API Management Service Contributor" 
$customRole = $contributorRole 
$customRole.Id = $null
$customRole.Name = "APIM New Portal Admin" 
$customRole.Description = "This role gives the user ability to log in to the new Developer portal as administrator" 
$customRole.Actions = "Microsoft.ApiManagement/service/users/token/action" 
$customRole.IsCustom = $true 
$customRole.AssignableScopes.Clear() 
$customRole.AssignableScopes.Add('/subscriptions/<subscription-id>') 
New-AzRoleDefinition -Role $customRole 
```
 
Po utworzeniu roli można ją przyznać dowolnemu użytkownikowi z sekcji **Access Control (IAM)** w Azure Portal. Przypisanie tej roli do użytkownika spowoduje przypisanie uprawnienia w zakresie usługi. Użytkownik będzie mógł generować tokeny SAS w imieniu *dowolnego użytkownika* w usłudze. Ta rola musi być co najmniej przypisana do administratora usługi. Następujące polecenie programu PowerShell ilustruje sposób przypisywania roli do użytkownika w najniższym zakresie, aby uniknąć udzielania `user1` niepotrzebnych uprawnień użytkownikowi: 

```powershell
New-AzRoleAssignment -SignInName "user1@contoso.com" -RoleDefinitionName "APIM New Portal Admin" -Scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1" 
```

Po przyznaniu uprawnień użytkownikowi użytkownik musi wylogować się i zalogować się ponownie do Azure Portal, aby nowe uprawnienia zostały wprowadzone.

## <a name="im-seeing-the-unable-to-start-the-portal-see-if-settings-are-specified-correctly--error"></a>Widzę `Unable to start the portal. See if settings are specified correctly (...)` błąd

Ten błąd jest wyświetlany, gdy wywołanie `GET` nie `https://<management-endpoint-hostname>/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.ApiManagement/service/xxx/contentTypes/document/contentItems/configuration?api-version=2018-06-01-preview` powiedzie się. Wywołanie jest wystawiane z przeglądarki przez interfejs administracyjny portalu.

Jeśli Twoja API Management znajduje się w sieci wirtualnej, zapoznaj się z pytaniem o łączność [z siecią wirtualną](#do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies).

Niepowodzenie wywołania może być również spowodowane przez certyfikat protokołu TLS/SSL, który jest przypisany do domeny niestandardowej i nie jest zaufany przez przeglądarkę. W celu ograniczenia ryzyka można usunąć niestandardową domenę punktu końcowego zarządzania, API Management wróci do domyślnego punktu końcowego z zaufanym certyfikatem.

## <a name="whats-the-browser-support-for-the-portal"></a>Jaka jest obsługa przeglądarki dla portalu?

| Przeglądarka                     | Obsługiwane       |
|-----------------------------|-----------------|
| Apple Safari                | Tak<sup>1</sup> |
| Google Chrome               | Tak<sup>1</sup> |
| Microsoft Edge              | Tak<sup>1</sup> |
| Microsoft Internet Explorer | Nie              |
| Mozilla Firefox             | Tak<sup>1</sup> |

 <small><sup>1</sup> Obsługiwane w dwóch najnowszych wersjach produkcyjnych.</small>

## <a name="local-development-of-my-self-hosted-portal-is-no-longer-working"></a>Lokalne tworzenie własnego portalu nie działa

Jeśli lokalna wersja portalu dla deweloperów nie może zapisywać ani pobierać informacji z konta magazynu lub wystąpienia API Management, tokeny SAS mogą wygasły. Można to naprawić, generując nowe tokeny. Aby uzyskać instrukcje, zapoznaj się z samouczkiem, [aby samodzielnie hostować portal deweloperów.](developer-portal-self-host.md#step-2-configure-json-files-static-website-and-cors-settings)

## <a name="how-can-i-remove-the-developer-portal-content-provisioned-to-my-api-management-service"></a>Jak usunąć zawartość portalu dla deweloperów aprowizowana w mojej API Management service?

Podaj wymagane parametry w `scripts.v3/cleanup.bat` skrypcie w repozytorium [GitHub](https://github.com/Azure/api-management-developer-portal)portalu deweloperów i uruchom skrypt

```sh
cd scripts.v3
.\cleanup.bat
cd ..
```

## <a name="how-do-i-enable-single-sign-on-sso-authentication-to-self-hosted-developer-portal"></a>Jak mogę włączyć uwierzytelnianie za pomocą logowania jednokrotnego (SSO) w własnym portalu dla deweloperów?

Poza innymi metodami uwierzytelniania portal dla deweloperów obsługuje logowanie jednokrotne. Aby uwierzytelnić się za pomocą tej metody, należy wykonać wywołanie metody przy użyciu `/signin-sso` tokenu w parametrze zapytania:

```html
https://contoso.com/signin-sso?token=[user-specific token]
```
### <a name="generate-user-tokens"></a>Generowanie tokenów użytkownika
Tokeny specyficzne dla użytkownika (w tym *tokeny* administratora) można wygenerować przy użyciu operacji Uzyskiwania tokenu dostępu współdzielonych [interfejsu API REST usługi API Management .](/rest/api/apimanagement/apimanagementrest/api-management-rest) [](/rest/api/apimanagement/2019-12-01/user/getsharedaccesstoken)

> [!NOTE]
> Token musi być zakodowany w adresie URL.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o nowym portalu dla deweloperów:

- [Uzyskiwanie dostępu do portalu dla deweloperów zarządzanych i dostosowywanie go](api-management-howto-developer-portal-customize.md)
- [Konfigurowanie własnej wersji portalu](developer-portal-self-host.md)
- [Implementowanie własnego widżetu](developer-portal-implement-widgets.md)

Przeglądaj inne zasoby:

- [Repozytorium GitHub z kodem źródłowym](https://github.com/Azure/api-management-developer-portal)

