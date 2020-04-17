---
title: Uwierzytelnianie i autoryzacja
description: Dowiedz się więcej o wbudowanej pomocy technicznej dotyczącej uwierzytelniania i autoryzacji w usłudze Azure App Service i usłudze Azure Functions oraz o tym, jak może pomóc w zabezpieczeniu aplikacji przed nieautoryzowanym dostępem.
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.topic: article
ms.date: 04/15/2020
ms.reviewer: mahender
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: a4ceed0d897f069a7895a3eb6b10c327566afbe5
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457862"
---
# <a name="authentication-and-authorization-in-azure-app-service-and-azure-functions"></a>Uwierzytelnianie i autoryzacja w usłudze Azure App Service i usłudze Azure Functions

> [!NOTE]
> Obecnie ASP.NET Core nie obsługuje obecnie wypełniania bieżącego użytkownika funkcją Uwierzytelnianie/Autoryzacja.
>

Usługa Azure App Service zapewnia wbudowaną obsługę uwierzytelniania i autoryzacji, dzięki czemu można logować się do użytkowników i uzyskiwać dostęp do danych, zapisując minimalny lub żaden kod w aplikacji sieci Web, interfejsie API RESTful i mobilnym zapleczu, a także [usłudze Azure Functions.](../azure-functions/functions-overview.md) W tym artykule opisano, jak usługa App Service pomaga uprościć uwierzytelnianie i autoryzację aplikacji.

Bezpieczne uwierzytelnianie i autoryzacja wymagają głębokiego zrozumienia zabezpieczeń, w tym federacji, szyfrowania, zarządzania [tokenami internetowymi JSON (JWT),](https://wikipedia.org/wiki/JSON_Web_Token) [typów dotacji](https://oauth.net/2/grant-types/)itd. Usługa App Service udostępnia te narzędzia, dzięki czemu można poświęcić więcej czasu i energii na dostarczanie wartości biznesowej dla klienta.

> [!IMPORTANT]
> Nie musisz używać tej funkcji do uwierzytelniania i autoryzacji. Możesz użyć dołączonych funkcji zabezpieczeń w wybranej platformie internetowej lub możesz napisać własne narzędzia. Należy jednak pamiętać, że [Chrome 80 wprowadza przełomowe zmiany w implementacji SameSite dla plików cookie](https://www.chromestatus.com/feature/5088147346030592) (data premiery około marca 2020), a niestandardowe zdalne uwierzytelnianie lub inne scenariusze, które opierają się na publikowaniu plików cookie między witrynami, mogą ulec zerwaniu, gdy przeglądarki klienckie Chrome zostaną zaktualizowane. Obejście jest złożone, ponieważ musi obsługiwać różne zachowania SameSite dla różnych przeglądarek. 
>
> Wersje ASP.NET Core 2.1 i nowsze obsługiwane przez App Service są już poprawione dla tej przełomowej zmiany i odpowiednio obsługują Chrome 80 i starsze przeglądarki. Ponadto ta sama poprawka dla ASP.NET Framework 4.7.2 jest wdrażana w wystąpieniach usługi App Service przez cały styczeń 2020. Aby uzyskać więcej informacji, w tym jak sprawdzić, czy aplikacja otrzymała poprawkę, zobacz [Aktualizacja pliku cookie usługi Azure App Service SameSite](https://azure.microsoft.com/updates/app-service-samesite-cookie-update/).
>

Aby uzyskać informacje dotyczące natywnych aplikacji mobilnych, zobacz [Uwierzytelnianie użytkowników i autoryzacja aplikacji mobilnych za pomocą usługi Azure App Service](../app-service-mobile/app-service-mobile-auth.md).

## <a name="how-it-works"></a>Jak to działa

Moduł uwierzytelniania i autoryzacji działa w tym samym piaskownicy co kod aplikacji. Gdy jest włączona, każde przychodzące żądanie HTTP przechodzi przez niego przed obsłużone przez kod aplikacji.

![](media/app-service-authentication-overview/architecture.png)

Ten moduł obsługuje kilka rzeczy dla aplikacji:

- Uwierzytelnia użytkowników przy użyciu określonego dostawcy
- Sprawdza poprawność, przechowuje i odświeża tokeny
- Zarządza uwierzytelnioną sesją
- Wstrzykuje informacje o tożsamości do nagłówków żądań

Moduł działa oddzielnie od kodu aplikacji i jest konfigurowany przy użyciu ustawień aplikacji. Nie są wymagane zestawy SDK, określone języki lub zmiany w kodzie aplikacji. 

### <a name="userapplication-claims"></a>Oświadczenia użytkownika/aplikacji

Dla wszystkich struktur języka App Service udostępnia oświadczenia w tokenie przychodzącym (czy to z uwierzytelnionego użytkownika końcowego lub aplikacji klienckiej) do kodu przez wstrzyknięcie ich do nagłówków żądań. W przypadku aplikacji ASP.NET 4.6 usługa App Service wypełnia [claimsprincipal.Current](/dotnet/api/system.security.claims.claimsprincipal.current) oświadczenia uwierzytelnionego użytkownika, dzięki czemu można wykonać `[Authorize]` standardowy wzorzec kodu .NET, w tym atrybut. Podobnie w przypadku aplikacji PHP usługa App `_SERVER['REMOTE_USER']` Service wypełnia zmienną. W przypadku aplikacji Java oświadczenia są [dostępne z serwera Tomcat](containers/configure-language-java.md#authenticate-users-easy-auth).

Dla [usługi Azure Functions](../azure-functions/functions-overview.md), `ClaimsPrincipal.Current` nie jest wypełniona dla kodu .NET, ale nadal `ClaimsPrincipal` można znaleźć oświadczenia użytkownika w nagłówkach żądań lub uzyskać obiekt z kontekstu żądania lub nawet za pośrednictwem parametru powiązania. Zobacz [pracy z tożsamościami klienta,](../azure-functions/functions-bindings-http-webhook-trigger.md#working-with-client-identities) aby uzyskać więcej informacji.

Aby uzyskać więcej informacji, zobacz [Dostęp do oświadczeń użytkowników](app-service-authentication-how-to.md#access-user-claims).

### <a name="token-store"></a>Magazyn tokenów

Usługa App Service udostępnia wbudowany magazyn tokenów, który jest repozytorium tokenów skojarzonych z użytkownikami aplikacji sieci web, interfejsów API lub natywnych aplikacji mobilnych. Po włączeniu uwierzytelniania u dowolnego dostawcy, ten magazyn tokenów jest natychmiast dostępny dla aplikacji. Jeśli kod aplikacji musi uzyskać dostęp do danych od tych dostawców w imieniu użytkownika, takich jak: 

- opublikuj na osi czasu uwierzytelnionego użytkownika na Facebooku
- odczytywania danych firmowych użytkownika za pomocą interfejsu API programu Microsoft Graph

Zazwyczaj należy napisać kod do zbierania, przechowywania i odświeżania tych tokenów w aplikacji. W magazynie tokenów po prostu pobrać tokeny, gdy ich potrzebujesz i [powiedzieć app service, aby odświeżyć je,](app-service-authentication-how-to.md#refresh-identity-provider-tokens) gdy staną się [nieprawidłowe.](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) 

Tokeny identyfikatorów, tokeny dostępu i tokeny odświeżania są buforowane dla uwierzytelnionej sesji i są dostępne tylko dla skojarzonego użytkownika.  

Jeśli nie musisz pracować z tokenami w aplikacji, możesz wyłączyć magazyn tokenów.

### <a name="logging-and-tracing"></a>Rejestrowanie i śledzenie

Jeśli [włączysz rejestrowanie aplikacji,](troubleshoot-diagnostic-logs.md)zobaczysz ślady uwierzytelniania i autoryzacji bezpośrednio w plikach dziennika. Jeśli zostanie wyświetlony błąd uwierzytelniania, którego się nie spodziewałeś, możesz wygodnie znaleźć wszystkie szczegóły, patrząc w istniejące dzienniki aplikacji. Jeśli śledzenie [żądań nie powiodło się,](troubleshoot-diagnostic-logs.md)można zobaczyć dokładnie, jaką rolę moduł uwierzytelniania i autoryzacji mógł odegrać w żądaniu, którego nie powiodło się. W dziennikach śledzenia poszukaj odwołań `EasyAuthModule_32/64`do modułu o nazwie . 

## <a name="identity-providers"></a>Dostawcy tożsamości

Usługa App Service używa [tożsamości federacyjnej,](https://en.wikipedia.org/wiki/Federated_identity)w której dostawca tożsamości innej firmy zarządza tożsamościami użytkowników i przepływem uwierzytelniania. Domyślnie dostępnych jest pięciu dostawców tożsamości: 

| Dostawca | Punkt końcowy logowania |
| - | - |
| [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) | `/.auth/login/aad` |
| [Konto Microsoft](../active-directory/develop/v2-overview.md) | `/.auth/login/microsoftaccount` |
| [Facebook](https://developers.facebook.com/docs/facebook-login) | `/.auth/login/facebook` |
| [Google](https://developers.google.com/identity/choose-auth) | `/.auth/login/google` |
| [Twitter](https://developer.twitter.com/en/docs/basics/authentication) | `/.auth/login/twitter` |

Po włączeniu uwierzytelniania i autoryzacji z jednym z tych dostawców, jego punkt końcowy logowania jest dostępny do uwierzytelniania użytkownika i sprawdzania poprawności tokenów uwierzytelniania od dostawcy. Możesz z łatwością udostępnić użytkownikom dowolną liczbę tych opcji logowania. Można również zintegrować innego dostawcę tożsamości lub [własne niestandardowe rozwiązanie tożsamości.][custom-auth]

## <a name="authentication-flow"></a>Przepływ uwierzytelniania

Przepływ uwierzytelniania jest taki sam dla wszystkich dostawców, ale różni się w zależności od tego, czy chcesz zalogować się przy użyciu sdk dostawcy:

- Bez sdk dostawcy: delegowania aplikacji federacyjne logowania do usługi App Service. Zazwyczaj dotyczy to aplikacji przeglądarki, które mogą przedstawiać użytkownikowi stronę logowania dostawcy. Kod serwera zarządza procesem logowania, więc jest również nazywany _przepływem ukierunkowanym na serwer_ lub _przepływem serwera._ Ten przypadek dotyczy aplikacji przeglądarki. Dotyczy to również aplikacji natywnych, które logują użytkowników przy użyciu sdk klienta aplikacji mobilnych, ponieważ SDK otwiera widok sieci Web, aby zalogować się za pomocą uwierzytelniania usługi App Service. 
- Za pomocą sdk dostawcy: aplikacja loguje użytkowników do dostawcy ręcznie, a następnie przesyła token uwierzytelniania do usługi App Service do weryfikacji. Zazwyczaj dotyczy to aplikacji bez przeglądarki, które nie mogą przedstawiać użytkownikowi strony logowania dostawcy. Kod aplikacji zarządza procesem logowania, więc jest również nazywany _przepływem kierowanym przez klienta_ lub _przepływem klienta._ Ten przypadek dotyczy interfejsów API REST, [usług Azure Functions](../azure-functions/functions-overview.md)i klientów przeglądarki JavaScript, a także aplikacji przeglądarki, które wymagają większej elastyczności w procesie logowania. Dotyczy to również natywnych aplikacji mobilnych, które logują użytkowników przy użyciu sdk dostawcy.

> [!NOTE]
> Wywołania z zaufanej aplikacji przeglądarki w usłudze App Service do innego interfejsu API REST w usłudze App Service lub [usłudze Azure Functions](../azure-functions/functions-overview.md) mogą być uwierzytelnione przy użyciu przepływu kierowanego na serwer. Aby uzyskać więcej informacji, zobacz [Dostosowywanie uwierzytelniania i autoryzacji w usłudze App Service](app-service-authentication-how-to.md).
>

W poniższej tabeli przedstawiono kroki przepływu uwierzytelniania.

| Krok | Bez sdk dostawcy | Z dostawcą SDK |
| - | - | - |
| 1. Zaloguj się do użytkownika | Przekierowuje `/.auth/login/<provider>`klienta do pliku . | Kod klienta loguje użytkownika bezpośrednio za pomocą sdk dostawcy i odbiera token uwierzytelniania. Aby uzyskać więcej informacji, zobacz dokumentację dostawcy. |
| 2. Uwierzytelnianie po uwierzytelnieniu | Dostawca przekierowuje klienta do `/.auth/login/<provider>/callback`. | Kod klienta [księguje](app-service-authentication-how-to.md#validate-tokens-from-providers) `/.auth/login/<provider>` token od dostawcy do sprawdzania poprawności. |
| 3. Ustanowienie uwierzytelnionej sesji | Usługa app service dodaje uwierzytelniony plik cookie do odpowiedzi. | Usługa App Service zwraca własny token uwierzytelniania do kodu klienta. |
| 4. Podawaj uwierzytelnione treści | Klient zawiera plik cookie uwierzytelniania w kolejnych żądaniach (automatycznie obsługiwany przez przeglądarkę). | Kod klienta przedstawia token `X-ZUMO-AUTH` uwierzytelniania w nagłówku (automatycznie obsługiwany przez sdki klienta aplikacji mobilnych). |

W przypadku przeglądarek klienckich usługa App Service może `/.auth/login/<provider>`automatycznie kierować wszystkich nieuwierzylicy do . Możesz również przedstawić użytkownikom jeden `/.auth/login/<provider>` lub więcej linków do logowania się do aplikacji przy użyciu wybranego dostawcy.

<a name="authorization"></a>

## <a name="authorization-behavior"></a>Zachowanie autoryzacji

W [witrynie Azure portal](https://portal.azure.com)można skonfigurować autoryzację usługi App Service z liczbą zachowań, gdy żądanie przychodzące nie jest uwierzytelnione.

![](media/app-service-authentication-overview/authorization-flow.png)

W poniższych nagłówkach opisano opcje.

### <a name="allow-anonymous-requests-no-action"></a>Zezwalaj na żądania anonimowe (brak akcji)

Ta opcja odracza autoryzację nieuwierzytynego ruchu do kodu aplikacji. W przypadku uwierzytelnionych żądań usługa App Service przekazuje również informacje uwierzytelniającego w nagłówkach HTTP. 

Ta opcja zapewnia większą elastyczność w obsłudze żądań anonimowych. Na przykład umożliwia [prezentowanie użytkownikom wielu dostawców logowania.](app-service-authentication-how-to.md#use-multiple-sign-in-providers) Jednak należy napisać kod. 

### <a name="allow-only-authenticated-requests"></a>Zezwalaj tylko na uwierzytelnione żądania

Ta opcja to **Zaloguj się za pomocą \<>dostawcy **. Usługa app service przekierowuje wszystkie anonimowe żądania do `/.auth/login/<provider>` wybranego dostawcy. Jeśli żądanie anonimowe pochodzi z natywnej `HTTP 401 Unauthorized`aplikacji mobilnej, zwrócona odpowiedź jest .

Za pomocą tej opcji nie trzeba pisać żadnego kodu uwierzytelniania w aplikacji. Autoryzacja drobna, taka jak autoryzacja specyficzna dla roli, może być obsługiwana przez inspekcję oświadczeń użytkownika (zobacz [Oświadczenia użytkowników programu Access).](app-service-authentication-how-to.md#access-user-claims)

> [!CAUTION]
> Ograniczenie dostępu w ten sposób ma zastosowanie do wszystkich wywołań aplikacji, co może nie być pożądane dla aplikacji, które chcą publicznie dostępnej strony głównej, jak w wielu aplikacjach jednostronicowych.

> [!NOTE]
> Uwierzytelnianie/autoryzacja było wcześniej znane jako Easy Auth.
>

## <a name="more-resources"></a>Więcej zasobów

[Samouczek: Uwierzytelnij i autoryzuj użytkowników end-to-end w usłudze Azure App Service (Windows)](app-service-web-tutorial-auth-aad.md)  
[Samouczek: Uwierzytelnij i autoryzuj użytkowników end-to-end w usłudze Azure App Service dla systemu Linux](containers/tutorial-auth-aad.md)  
[Dostosowywanie uwierzytelniania i autoryzacji w usłudze App Service](app-service-authentication-how-to.md)
[.NET Core integracji usługi Azure AppService EasyAuth (3rd party)](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth)
[Uzyskiwanie uwierzytelniania usługi Azure App Service pracy z .NET Core (3rd party)](https://github.com/kirkone/KK.AspNetCore.EasyAuthAuthentication)

Przewodniki dotyczące inaucyjek specyficznych dla dostawcy:

* [Jak skonfigurować aplikację do używania logowania w usłudze Azure Active Directory][AAD]
* [Jak skonfigurować aplikację do używania logowania usługi Facebook][Facebook]
* [Jak skonfigurować aplikację do używania logowania usługi Google][Google]
* [Jak skonfigurować aplikację do używania logowania za pomocą konta Microsoft][MSA]
* [Jak skonfigurować aplikację do używania logowania usługi Twitter][Twitter]
* [Jak: Używanie uwierzytelniania niestandardowego dla aplikacji][custom-auth]

[AAD]: configure-authentication-provider-aad.md
[Facebook]: configure-authentication-provider-facebook.md
[Google]: configure-authentication-provider-google.md
[MSA]: configure-authentication-provider-microsoft.md
[Twitter]: configure-authentication-provider-twitter.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
