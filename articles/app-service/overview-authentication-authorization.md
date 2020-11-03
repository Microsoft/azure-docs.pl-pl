---
title: Uwierzytelnianie i autoryzacja
description: Dowiedz się więcej o wbudowanej obsłudze uwierzytelniania i autoryzacji w Azure App Service i Azure Functions oraz sposobie zabezpieczania aplikacji przed nieautoryzowanym dostępem.
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.topic: article
ms.date: 07/08/2020
ms.reviewer: mahender
ms.custom: seodec18, fasttrack-edit, has-adal-ref
ms.openlocfilehash: 09460e4e38ea1842d58ecf3a9b3cd00a072c271e
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93286911"
---
# <a name="authentication-and-authorization-in-azure-app-service-and-azure-functions"></a>Uwierzytelnianie i autoryzacja w Azure App Service i Azure Functions

Azure App Service zapewnia wbudowaną obsługę uwierzytelniania i autoryzacji, dzięki czemu możesz zalogować użytkowników i uzyskać dostęp do danych, pisząc kod minimalny lub bez kodu w aplikacji sieci Web, interfejsie API RESTful i zapleczu mobilnego, a także [Azure Functions](../azure-functions/functions-overview.md). W tym artykule opisano sposób, w jaki App Service upraszczają uwierzytelnianie i autoryzację aplikacji.

Bezpieczne uwierzytelnianie i autoryzacja wymagają dokładnego poznania zabezpieczeń, w tym Federacji, szyfrowania, zarządzania [tokenami sieci Web JSON (JWT)](https://wikipedia.org/wiki/JSON_Web_Token) , [typów dotacji](https://oauth.net/2/grant-types/)i tak dalej. App Service udostępnia te narzędzia, dzięki czemu można poświęcać więcej czasu i energii na zapewnienie klientom wartości biznesowej.

> [!IMPORTANT]
> Nie jest wymagane korzystanie z tej funkcji do uwierzytelniania i autoryzacji. Możesz użyć wbudowanych funkcji zabezpieczeń w ramach wybranej platformy sieci Web lub można napisać własne narzędzia. Należy jednak pamiętać, że program [chrome 80 wprowadza istotne zmiany w implementacji SameSite dla plików cookie](https://www.chromestatus.com/feature/5088147346030592) (Data wydania około marca 2020), a niestandardowe uwierzytelnianie zdalne lub inne scenariusze, które opierają się na księgowaniu plików cookie między lokacjami, mogą ulec przerwaniu podczas aktualizowania przeglądarek programu Chrome. Obejście jest złożone, ponieważ musi obsługiwać różne zachowania SameSite dla różnych przeglądarek. 
>
> W ASP.NET Core 2,1 i nowszych wersjach hostowanych przez App Service są już zainstalowane poprawki dla tej istotnej zmiany i obsługują odpowiednio program Chrome 80 i starsze przeglądarki. Ponadto ta sama poprawka dla programu ASP.NET Framework 4.7.2 została wdrożona w App Service wystąpieniach w styczniu 2020. Aby uzyskać więcej informacji, zobacz [Azure App Service SameSite cookie Update](https://azure.microsoft.com/updates/app-service-samesite-cookie-update/).
>

> [!NOTE]
> Funkcja uwierzytelniania/autoryzacji jest również czasami określana jako "Łatwa autoryzacja".

> [!NOTE]
> Włączenie tej funkcji spowoduje, że **wszystkie** niezabezpieczone żądania HTTP do aplikacji będą automatycznie przekierowywane do protokołu HTTPS, niezależnie od ustawienia konfiguracji App Service [wymuszania protokołu HTTPS](configure-ssl-bindings.md#enforce-https). W razie potrzeby można je wyłączyć za pomocą `requireHttps` Ustawienia w [pliku konfiguracyjnym ustawień uwierzytelniania](app-service-authentication-how-to.md#configuration-file-reference), ale należy pamiętać o zapewnieniu, że tokeny zabezpieczające nie są przekazywane za pośrednictwem niezabezpieczonych połączeń HTTP.

Aby uzyskać informacje specyficzne dla natywnych aplikacji mobilnych, zobacz [uwierzytelnianie użytkowników i autoryzacja dla aplikacji mobilnych za pomocą Azure App Service](/previous-versions/azure/app-service-mobile/app-service-mobile-auth).

## <a name="how-it-works"></a>Jak to działa

### <a name="on-windows"></a>W systemie Windows

Moduł uwierzytelniania i autoryzacji jest uruchamiany w tej samej piaskownicy, w której znajduje się kod aplikacji. Gdy jest włączona, każde przychodzące żądanie HTTP przechodzi przez niego przed przekazaniem przez kod aplikacji.

![Diagram architektury pokazujący żądania przechwytywane przez proces w piaskownicy lokacji, który współdziała z dostawcami tożsamości przed zezwoleniem na ruch do wdrożonej lokacji](media/app-service-authentication-overview/architecture.png)

Ten moduł obsługuje kilka rzeczy dla aplikacji:

- Uwierzytelnia użytkowników z określonym dostawcą
- Weryfikuje, zapisuje i odświeża tokeny
- Zarządza sesją uwierzytelnioną
- Wprowadza informacje o tożsamości do nagłówków żądań

Moduł zostanie uruchomiony niezależnie od kodu aplikacji i skonfigurowany przy użyciu ustawień aplikacji. Nie są wymagane żadne zestawy SDK, określone Języki ani zmiany w kodzie aplikacji. 

### <a name="on-containers"></a>W kontenerach

Moduł uwierzytelniania i autoryzacji jest uruchamiany w oddzielnym kontenerze odizolowanym od kodu aplikacji. Za pomocą tego, co jest znane jako [wzorzec ambasadora](/azure/architecture/patterns/ambassador), współdziała z ruchem przychodzącym w celu przeprowadzenia podobnej funkcjonalności jak w systemie Windows. Ponieważ nie działa on w procesie, nie jest możliwa Bezpośrednia integracja z określonymi strukturami języka; jednak odpowiednie informacje wymagane przez aplikację są przesyłane za pośrednictwem nagłówków żądań, jak wyjaśniono poniżej.

### <a name="userapplication-claims"></a>Oświadczenia użytkownika/aplikacji

W przypadku wszystkich platform językowych App Service wykonuje oświadczenia w tokenie przychodzącym (niezależnie od tego, czy są one od uwierzytelnionego użytkownika końcowego lub aplikacji klienckiej) dostępne dla kodu, wprowadzając je do nagłówków żądania. W przypadku aplikacji ASP.NET 4,6 App Service wypełnia [ClaimsPrincipal. Current](/dotnet/api/system.security.claims.claimsprincipal.current) przy użyciu oświadczeń uwierzytelnionego użytkownika, więc można przestrzegać standardowego wzorca kodu .NET, w tym `[Authorize]` atrybutu. Podobnie w przypadku aplikacji PHP App Service wypełnia `_SERVER['REMOTE_USER']` zmienną. W przypadku aplikacji Java oświadczenia są [dostępne z Tomcat serwletu](configure-language-java.md#authenticate-users-easy-auth).

W [Azure Functions](../azure-functions/functions-overview.md)przypadku Azure Functions `ClaimsPrincipal.Current` nie jest wypełniony dla kodu platformy .NET, ale nadal można znaleźć oświadczenia użytkownika w nagłówkach żądania lub pobrać `ClaimsPrincipal` obiekt z kontekstu żądania lub nawet za pośrednictwem parametru powiązania. Aby uzyskać więcej informacji, zobacz [Praca z tożsamościami klientów](../azure-functions/functions-bindings-http-webhook-trigger.md#working-with-client-identities) .

Aby uzyskać więcej informacji, zobacz [Uzyskiwanie dostępu do oświadczeń użytkowników](app-service-authentication-how-to.md#access-user-claims).

> [!NOTE]
> W tej chwili ASP.NET Core nie obsługuje obecnie wypełniania bieżącego użytkownika za pomocą funkcji uwierzytelniania/autoryzacji. Niemniej jednak w przypadku niektórych [innych firm składniki pośredniczące programu Open Source](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth) mają być przydatne do wypełnienia tej przerwy.
>

### <a name="token-store"></a>Magazyn tokenów

App Service udostępnia wbudowany magazyn tokenów, który jest repozytorium tokenów skojarzonych z użytkownikami aplikacji sieci Web, interfejsów API lub natywnych aplikacji mobilnych. Po włączeniu uwierzytelniania dla dowolnego dostawcy ten magazyn tokenów jest natychmiast dostępny dla aplikacji. Jeśli kod aplikacji musi uzyskać dostęp do danych od tych dostawców w imieniu użytkownika, na przykład: 

- Opublikuj na osi czasu w usłudze Facebook uwierzytelnionego użytkownika
- Odczytywanie firmowych danych użytkownika przy użyciu interfejsu API Microsoft Graph

Zwykle należy napisać kod, aby zbierać, przechowywać i odświeżać te tokeny w aplikacji. W przypadku magazynu tokenów wystarczy [pobrać tokeny](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) , gdy są potrzebne, i [poinformowanie App Service o konieczności ich odświeżenia](app-service-authentication-how-to.md#refresh-identity-provider-tokens) , gdy staną się nieprawidłowe. 

Tokeny identyfikatora, tokeny dostępu i tokeny odświeżania są buforowane dla sesji uwierzytelnionej i są dostępne tylko dla skojarzonego użytkownika.  

Jeśli nie musisz korzystać z tokenów w aplikacji, możesz wyłączyć magazyn tokenów na stronie **uwierzytelnianie/autoryzacja** aplikacji.

### <a name="logging-and-tracing"></a>Rejestrowanie i śledzenie

Po [włączeniu rejestrowania aplikacji](troubleshoot-diagnostic-logs.md)będą widoczne ślady uwierzytelniania i autoryzacji bezpośrednio w plikach dziennika. Jeśli zobaczysz nieoczekiwany błąd uwierzytelniania, możesz wygodnie znaleźć wszystkie szczegóły, przeglądając istniejące dzienniki aplikacji. W przypadku włączenia [śledzenia nieudanych żądań](troubleshoot-diagnostic-logs.md)można zobaczyć dokładnie rolę modułu uwierzytelnianie i autoryzacja w nieprawidłowym żądaniu. W dziennikach śledzenia poszukaj odwołań do modułu o nazwie `EasyAuthModule_32/64` . 

## <a name="identity-providers"></a>Dostawcy tożsamości

App Service używa [tożsamości federacyjnej](https://en.wikipedia.org/wiki/Federated_identity), w której dostawca tożsamości innej firmy zarządza tożsamościami użytkowników i przepływem uwierzytelniania. Pięć dostawców tożsamości jest dostępnych domyślnie: 

| Dostawca | Punkt końcowy logowania |
| - | - |
| [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) | `/.auth/login/aad` |
| [Konto Microsoft](../active-directory/develop/v2-overview.md) | `/.auth/login/microsoftaccount` |
| [Facebook](https://developers.facebook.com/docs/facebook-login) | `/.auth/login/facebook` |
| [Google](https://developers.google.com/identity/choose-auth) | `/.auth/login/google` |
| [Twitter](https://developer.twitter.com/en/docs/basics/authentication) | `/.auth/login/twitter` |
| Dowolny dostawca [połączenia OpenID Connect](https://openid.net/connect/) (wersja zapoznawcza) | `/.auth/login/<providerName>` |

Po włączeniu uwierzytelniania i autoryzacji jednego z tych dostawców, jego punkt końcowy logowania jest dostępny do uwierzytelnienia użytkownika i do weryfikacji tokenów uwierzytelniania od dostawcy. Możesz zapewnić użytkownikom dowolną liczbę tych opcji logowania.

Istnieje [Starsza ścieżka rozszerzalności][custom-auth] dla integracji z innymi dostawcami tożsamości lub niestandardowym rozwiązaniem uwierzytelniania, ale nie jest to zalecane. Zamiast tego należy rozważyć użycie usługi OpenID Connect Connect.

## <a name="authentication-flow"></a>Przepływ uwierzytelniania

Przepływ uwierzytelniania jest taki sam dla wszystkich dostawców, ale różni się w zależności od tego, czy chcesz zalogować się przy użyciu zestawu SDK dostawcy:

- Bez zestawu SDK dostawcy: aplikacja deleguje logowanie federacyjne do App Service. Jest to zazwyczaj przypadek z aplikacjami przeglądarki, które mogą przedstawić użytkownikowi stronę logowania dostawcy. Kod serwera zarządza procesem logowania, więc jest również nazywany _przepływem skierowanym do serwera_ lub _przepływem serwera_. Ta sprawa dotyczy aplikacji przeglądarki. Dotyczy to również natywnych aplikacji, które logują użytkowników przy użyciu zestawu SDK klienta Mobile Apps, ponieważ zestaw SDK otwiera widok sieci Web służący do logowania użytkowników przy użyciu uwierzytelniania App Service. 
- Z zestawem SDK dostawcy: aplikacja bezterminowo loguje użytkowników do dostawcy, a następnie przesyła token uwierzytelniania do App Service na potrzeby weryfikacji. Jest to zazwyczaj przypadek z aplikacjami bez przeglądarki, które nie mogą przedstawić użytkownikowi strony logowania dostawcy. Kod aplikacji zarządza procesem logowania, więc jest również nazywany _przepływem kierowanym przez klienta_ lub _przepływem klienta_. Ten przypadek dotyczy interfejsów API REST, [Azure Functions](../azure-functions/functions-overview.md)i klientów przeglądarki JavaScript, a także aplikacji przeglądarki, które potrzebują większej elastyczności w procesie logowania. Dotyczy to również natywnych aplikacji mobilnych, które logują użytkowników przy użyciu zestawu SDK dostawcy.

> [!NOTE]
> Wywołania z aplikacji zaufanej przeglądarki w App Service do innego interfejsu API REST w programie App Service lub [Azure Functions](../azure-functions/functions-overview.md) można uwierzytelnić przy użyciu przepływu ukierunkowanego na serwer. Aby uzyskać więcej informacji, zobacz [Dostosowywanie uwierzytelniania i autoryzacji w App Service](app-service-authentication-how-to.md).
>

W poniższej tabeli przedstawiono kroki przepływu uwierzytelniania.

| Krok | Bez zestawu SDK dostawcy | Z zestawem SDK dostawcy |
| - | - | - |
| 1. Podpisz użytkownika w | Przekierowuje klienta do programu `/.auth/login/<provider>` . | Kod klienta podpisuje użytkownika bezpośrednio przy użyciu zestawu SDK dostawcy i odbiera token uwierzytelniania. Aby uzyskać więcej informacji, zobacz dokumentację dostawcy. |
| 2. po uwierzytelnieniu | Dostawca przekierowuje klienta do programu `/.auth/login/<provider>/callback` . | Kod klienta [zapisuje token od dostawcy](app-service-authentication-how-to.md#validate-tokens-from-providers) do `/.auth/login/<provider>` walidacji. |
| 3. ustanawianie sesji uwierzytelnionej | App Service dodaje uwierzytelniony plik cookie do odpowiedzi. | App Service zwraca swój własny token uwierzytelniania do kodu klienta. |
| 4. Obsługuj uwierzytelnioną zawartość | Klient zawiera plik cookie uwierzytelniania w kolejnych żądaniach (automatycznie obsłużonych przez przeglądarkę). | Kod klienta przedstawia token uwierzytelniania w `X-ZUMO-AUTH` nagłówku (automatycznie obsłużony przez Mobile Apps zestawy SDK klienta). |

W przypadku przeglądarek klienta App Service może automatycznie kierować wszystkich nieuwierzytelnionych użytkowników do programu `/.auth/login/<provider>` . Możesz również przedstawić użytkownikom linki do jednego lub kilku `/.auth/login/<provider>` linków, aby zalogować się do aplikacji przy użyciu wybranego przez siebie dostawcy.

<a name="authorization"></a>

## <a name="authorization-behavior"></a>Zachowanie autoryzacji

W [Azure Portal](https://portal.azure.com)można skonfigurować autoryzację App Service z liczbą zachowań, gdy żądanie przychodzące nie zostanie uwierzytelnione.

![Zrzut ekranu przedstawiający listę rozwijaną "Akcja do wykonania, gdy żądanie nie zostało uwierzytelnione"](media/app-service-authentication-overview/authorization-flow.png)

Poniższe nagłówki opisują opcje.

### <a name="allow-anonymous-requests-no-action"></a>Zezwalaj na żądania anonimowe (bez akcji)

Ta opcja umożliwia rozliczanie autoryzacji nieuwierzytelnionego ruchu do kodu aplikacji. W przypadku żądań uwierzytelnionych App Service również przekazuje informacje o uwierzytelnianiu w nagłówkach HTTP. 

Ta opcja zapewnia większą elastyczność obsługi żądań anonimowych. Na przykład umożliwia [prezentowanie użytkownikom wielu dostawców logowania](app-service-authentication-how-to.md#use-multiple-sign-in-providers) . Jednak należy napisać kod. 

### <a name="allow-only-authenticated-requests"></a>Zezwalaj tylko na uwierzytelnione żądania

Opcja jest **Logowanie za pomocą programu \<provider>**. App Service przekierowuje wszystkie anonimowe żądania do wybranego `/.auth/login/<provider>` dostawcy. Jeśli żądanie anonimowe pochodzi z natywnej aplikacji mobilnej, zwrócona odpowiedź to `HTTP 401 Unauthorized` .

W przypadku tej opcji nie trzeba pisać kodu uwierzytelniania w aplikacji. Bardziej precyzyjne uwierzytelnianie, takie jak autoryzacja specyficzna dla ról, może być obsługiwane przez sprawdzenie oświadczeń użytkownika (zobacz [dostęp do oświadczeń użytkowników](app-service-authentication-how-to.md#access-user-claims)).

> [!CAUTION]
> Ograniczenie dostępu w ten sposób dotyczy wszystkich wywołań aplikacji, które mogą nie być odpowiednie dla aplikacji, które chcą korzystać z publicznie dostępnej strony głównej, tak jak w przypadku aplikacji jednostronicowych.

> [!NOTE]
> Domyślnie każdy użytkownik w dzierżawie usługi Azure AD może zażądać tokenu dla aplikacji z usługi Azure AD. Można [skonfigurować aplikację w usłudze Azure AD](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md) , jeśli chcesz ograniczyć dostęp do aplikacji do zdefiniowanego zestawu użytkowników.

## <a name="more-resources"></a>Dodatkowe zasoby

* [Samouczek: uwierzytelnianie i Autoryzowanie użytkowników na zakończenie w Azure App Service (Windows)](tutorial-auth-aad.md)  
* [Samouczek: uwierzytelnianie i Autoryzowanie użytkowników w Azure App Service dla systemu Linux](./tutorial-auth-aad.md?pivots=platform-linux%3fpivots%3dplatform-linux)  
* [Dostosuj uwierzytelnianie i autoryzację w App Service](app-service-authentication-how-to.md)
* [Integracja z platformą .NET Core w usłudze Azure AppService EasyAuth (trzecia firma)](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth)
* [Pobieranie Azure App Service uwierzytelniania podczas pracy z platformą .NET Core (inna firma)](https://github.com/kirkone/KK.AspNetCore.EasyAuthAuthentication)

Przewodniki z poszczególnymi dostawcami:

* [Jak skonfigurować aplikację do używania logowania w usłudze Azure Active Directory][AAD]
* [Jak skonfigurować aplikację do używania logowania usługi Facebook][Facebook]
* [Jak skonfigurować aplikację do używania logowania usługi Google][Google]
* [Jak skonfigurować aplikację do używania logowania za pomocą konta Microsoft][MSA]
* [Jak skonfigurować aplikację do używania logowania usługi Twitter][Twitter]
* [Jak skonfigurować aplikację do używania dostawcy połączenia usługi OpenID Connect na potrzeby logowania (wersja zapoznawcza)][OIDC]

[AAD]: configure-authentication-provider-aad.md
[Facebook]: configure-authentication-provider-facebook.md
[Google]: configure-authentication-provider-google.md
[MSA]: configure-authentication-provider-microsoft.md
[Twitter]: configure-authentication-provider-twitter.md
[OIDC]: configure-authentication-provider-openid-connect.md

[custom-auth]: /previous-versions/azure/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk#custom-auth

[ADAL-Android]: /previous-versions/azure/app-service-mobile/app-service-mobile-android-how-to-use-client-library#adal
[ADAL-iOS]: /previous-versions/azure/app-service-mobile/app-service-mobile-ios-how-to-use-client-library#adal
[ADAL-dotnet]: /previous-versions/azure/app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library#adal
