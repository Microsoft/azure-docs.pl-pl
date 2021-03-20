---
title: Informacje o dzieleniu zmian Azure Active Directory
description: Dowiedz się więcej na temat zmian wprowadzonych w protokołach usługi Azure AD, które mogą mieć wpływ na aplikację.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: reference
ms.date: 2/22/2021
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: c5e7f556f37a1d6d53e0a938490f1099a7be776a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101647425"
---
# <a name="whats-new-for-authentication"></a>Co nowego w uwierzytelnianiu?

> Otrzymuj powiadomienia o aktualizacjach tej strony, wklejając ten adres URL do czytnika kanału informacyjnego RSS:<br/>`https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us`

System uwierzytelniania zmienia i dodaje funkcje na bieżąco w celu poprawy bezpieczeństwa i zgodności ze standardami. Aby zachować aktualność najnowszych zmian, ten artykuł zawiera informacje o następujących szczegółach:

- Najnowsze funkcje
- Znane problemy
- Zmiany protokołu
- Funkcje uznane za przestarzałe

> [!TIP]
> Ta strona jest regularnie aktualizowana, więc często pojawia się. O ile nie zaznaczono inaczej, te zmiany są wprowadzane tylko dla nowo zarejestrowanych aplikacji.

## <a name="upcoming-changes"></a>Nadchodzące zmiany

### <a name="conditional-access-will-only-trigger-for-explicitly-requested-scopes"></a>Dostęp warunkowy zostanie wyzwolony tylko dla jawnie żądanych zakresów

**Data wprowadzenia**: marzec 2021

**Wpływ na punkty końcowe**: v 2.0

**Wpływ na protokół**: wszystkie przepływy używające [dynamicznej zgody](v2-permissions-and-consent.md#requesting-individual-user-consent)

Aplikacje korzystające z funkcji dynamicznej zgody już dzisiaj otrzymują wszystkie uprawnienia, dla których są wyrażane zgodę, nawet jeśli nie zażądano ich w `scope` parametrze według nazwy.  Może to spowodować żądanie aplikacji, na przykład tylko `user.read` , ale z zgodą na `files.read` , aby zostać zmuszony do przekazania warunkowego dostępu przypisanego do tego `files.read` uprawnienia. 

Aby zmniejszyć liczbę niepotrzebnych monitów dostępu warunkowego, usługa Azure AD zmienia sposób, w jaki zakresy niewymagane są dostarczane do aplikacji tak, że tylko jawnie żądane zakresy wyzwalają dostęp warunkowy. Ta zmiana może spowodować, że aplikacje oparte na poprzednim zachowaniu usługi Azure AD (tj. udostępnieniu wszystkich uprawnień nawet wtedy, gdy nie zażądały), ponieważ tokeny, których żądają, nie będą miały uprawnień.

Aplikacje będą teraz otrzymywać tokeny dostępu z mieszanymi uprawnieniami w tym zakresie, a także z tymi, które są wyrażające zgodę na to, że nie wymagają monitów o dostęp warunkowy.  Zakresy tokenu dostępu są odzwierciedlone w `scope` parametrze odpowiedzi tokenu. 

**Przykłady**

Aplikacja ma zgodę na `user.read` , `files.readwrite` i `tasks.read` . `files.readwrite` ma zastosowane zasady dostępu warunkowego, natomiast pozostałe dwa nie. Jeśli aplikacja wykonuje żądanie tokenu dla programu `scope=user.read` , a aktualnie zalogowany użytkownik nie przeszedł żadnych zasad dostępu warunkowego, wynikowy token będzie dotyczyć `user.read` `tasks.read` uprawnień i. `tasks.read` jest uwzględniony, ponieważ aplikacja ma do niej zgodę i nie wymaga wymuszania zasad dostępu warunkowego. 

Jeśli aplikacja zostanie zażądana `scope=files.readwrite` , dostęp warunkowy wymagany przez dzierżawcę zostanie wyzwolony, wymuszając aplikacji wyświetlanie interakcyjnego monitu o uwierzytelnienie, w którym można spełnić zasady dostępu warunkowego.  Zwrócony token będzie zawierać wszystkie trzy zakresy. 

Jeśli aplikacja następnie wykonuje jedno żądanie dla dowolnego z trzech zakresów (Powiedz, `scope=tasks.read` ), usługa Azure AD zobaczy, że użytkownik zakończył już zasady dostępu warunkowego, które są potrzebne do `files.readwrite` , i ponownie wystawia token ze wszystkimi trzema uprawnieniami. 


## <a name="may-2020"></a>Maj 2020 r.

### <a name="azure-government-endpoints-are-changing"></a>Azure Government punkty końcowe są zmieniane

**Data wprowadzenia**: maj piąty (końcowa 2020 czerwca) 

**Wpływ na punkty końcowe**: wszystkie

**Wpływ na protokół**: wszystkie przepływy

1 czerwca 2018 Urząd oficjalnych Azure Active Directory (AAD) dla Azure Government zmieniony z `https://login-us.microsoftonline.com` na `https://login.microsoftonline.us` . Ta zmiana została również zastosowana do Microsoft 365 i DoD w serwisie zatoce, które Azure Government usługi AAD. Jeśli jesteś członkiem aplikacji w ramach dzierżawy dla instytucji rządowych Stanów Zjednoczonych, musisz zaktualizować aplikację, aby zalogować użytkowników w `.us` punkcie końcowym.  

Począwszy od 5 maja, usługa Azure AD zacznie wymuszać zmianę punktu końcowego, blokując Logowanie użytkowników w aplikacjach hostowanych w dzierżawach dla instytucji rządowych Stanów Zjednoczonych przy użyciu publicznego punktu końcowego ( `microsoftonline.com` ).  Aplikacje, których dotyczy problem, rozpoczną wyświetlanie błędu `AADSTS900439`  -  `USGClientNotSupportedOnPublicEndpoint` . Ten błąd oznacza, że aplikacja próbuje zalogować się do użytkownika rządu USA w punkcie końcowym chmury publicznej. Jeśli Twoja aplikacja znajduje się w dzierżawie chmury publicznej i jest przeznaczona do obsługi użytkowników w Stanach Zjednoczonych, musisz [zaktualizować aplikację, aby obsługiwała ją jawnie](./authentication-national-cloud.md). Może to wymagać utworzenia nowej rejestracji aplikacji w chmurze dla instytucji rządowych USA. 

Egzekwowanie tej zmiany zostanie wykonane przy użyciu stopniowego wdrażania w zależności od tego, jak często użytkownicy z chmury rządowej Stanów Zjednoczonych zalogują się do aplikacji aplikacja — aplikacje dla instytucji rządowych STANów Zjednoczonych często zobaczą wymuszanie, a aplikacje często używane przez użytkowników rządów USA będą musiały zostać zastosowane. Oczekujemy, że wymuszanie zakończy się we wszystkich aplikacjach w czerwcu 2020. 

Aby uzyskać więcej informacji, zobacz [wpis w blogu Azure Government dotyczący tej migracji](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/). 

## <a name="march-2020"></a>Marzec 2020 r.

### <a name="user-passwords-will-be-restricted-to-256-characters"></a>Hasła użytkowników będą ograniczone do 256 znaków.

**Data wprowadzenia**: 13 marca 2020

**Wpływ na punkty końcowe**: wszystkie

**Wpływ na protokół**: wszystkie przepływy użytkownika.

Użytkownicy z hasłami dłuższymi niż 256 znaków, które logują się bezpośrednio do usługi Azure AD (w przeciwieństwie do federacyjnych dostawcy tożsamości takich jak ADFS), nie będą mogli zalogować się od 13 marca 2020, a zamiast tego zostanie wyświetlony monit o zresetowanie hasła.  Administratorzy mogą otrzymywać żądania, aby pomóc Ci zresetować hasło użytkownika.

Błąd w dziennikach logowania to AADSTS 50052: InvalidPasswordExceedsMaxLength

Komunikat: `The password entered exceeds the maximum length of 256. Please reach out to your admin to reset the password.`

Korygowania

Użytkownik nie może się zalogować, ponieważ jego hasło przekracza dozwoloną maksymalną długość. Należy skontaktować się z administratorem w celu zresetowania hasła. Jeśli SSPR jest włączona dla swojej dzierżawy, może zresetować swoje hasło, wykonując łącze "nie pamiętasz hasła".



## <a name="february-2020"></a>Luty 2020 r.

### <a name="empty-fragments-will-be-appended-to-every-http-redirect-from-the-login-endpoint"></a>Puste fragmenty zostaną dołączone do każdego przekierowania HTTP z punktu końcowego logowania.

**Data wprowadzenia**: 8 lutego 2020

**Wpływ na punkty końcowe**: 1.0 i v 2.0

**Wpływ na protokół**: przepływy OAuth i OIDC, które używają response_type = Query — obejmuje to [przepływ kodu autoryzacji](v2-oauth2-auth-code-flow.md) w niektórych przypadkach i [niejawny przepływ](v2-oauth2-implicit-grant-flow.md).

Po wysłaniu odpowiedzi uwierzytelniania z login.microsoftonline.com do aplikacji za pośrednictwem przekierowania HTTP usługa dołączy pusty fragment do adresu URL odpowiedzi.  Pozwala to zapobiec atakom klasy w celu przekierowania przez zagwarantowanie, że przeglądarka wyczyści wszystkie istniejące fragmenty w żądaniu uwierzytelniania.  Żadne aplikacje nie powinny być zależne od tego zachowania.


## <a name="august-2019"></a>Sierpień 2019 r.

### <a name="post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored"></a>Semantyka formularza POST zostanie wymuszona z większą ilością miejsc, a cudzysłowy zostaną zignorowane

**Data wprowadzenia**: 2 września 2019

**Wpływ na punkty końcowe**: 1.0 i v 2.0

**Wpływ na protokół**: wpis wszędzie jest używany ([poświadczenia klienta](./v2-oauth2-client-creds-grant-flow.md), [wykup kodu autoryzacji](./v2-oauth2-auth-code-flow.md), [ROPC](./v2-oauth-ropc.md), [OBO](./v2-oauth2-on-behalf-of-flow.md)i [wykup tokenów](./v2-oauth2-auth-code-flow.md#refresh-the-access-token))

Począwszy od tygodnia 9/2, żądania uwierzytelniania używające metody POST będą weryfikowane przy użyciu bardziej rygorystycznych standardów protokołu HTTP.  W przypadku opcji spacje i podwójne cudzysłowy (") nie będą już usuwane z wartości formularza żądania. Te zmiany nie są oczekiwane w celu podziału istniejących klientów i zapewniają niezawodne Obsługiwanie żądań wysyłanych do usługi Azure AD za każdym razem. W przyszłości (Zobacz powyżej) planujemy dodatkowo odrzucanie zduplikowanych parametrów i ignorowanie BOM w ramach żądań.

Przykład:

Dzisiaj `?e=    "f"&g=h` jest analizowane identycznie tak, jak to `?e=f&g=h` możliwe `e`  ==  `f` .  W przypadku tej zmiany teraz będzie ona analizowana w taki sposób, aby nie był to `e`  ==  `    "f"` prawidłowy argument, a żądanie mogłoby się nie powieść.


## <a name="july-2019"></a>Lipiec 2019 r.

### <a name="app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant"></a>Tokeny tylko do aplikacji dla aplikacji z jedną dzierżawą są wystawiane tylko wtedy, gdy aplikacja kliencka istnieje w dzierżawie zasobu

**Data wprowadzenia**: 26 lipca 2019

**Wpływ na punkty końcowe**: [1.0](../azuread-dev/v1-oauth2-client-creds-grant-flow.md) i v [2.0](./v2-oauth2-client-creds-grant-flow.md)

**Wpływ na protokół**: [poświadczenia klienta (tokeny tylko dla aplikacji)](../azuread-dev/v1-oauth2-client-creds-grant-flow.md)

Nastąpiła zmiana zabezpieczeń na żywo 26 lipca, która zmienia sposób, w jaki są wydawane tokeny tylko dla aplikacji (za pośrednictwem przyznanych poświadczeń klienta). Wcześniej aplikacje mogły uzyskać tokeny wywołujące dowolną inną aplikację, niezależnie od obecności w dzierżawie lub rolach, które zostały wysłane do danej aplikacji.  To zachowanie zostało zaktualizowane, tak aby w przypadku zasobów (nazywanych czasem interfejsem API sieci Web) ustawić jedną dzierżawę (domyślnie), aplikacja kliencka musi znajdować się w dzierżawie zasobów.  Należy zauważyć, że istniejąca zgoda między klientem i interfejsem API nadal nie jest wymagana, a aplikacje nadal powinny przeprowadzać własne testy autoryzacji, aby upewnić się, że `roles` występuje żądanie i zawiera oczekiwaną wartość dla interfejsu API.

Komunikat o błędzie dla tego scenariusza jest obecnie stanem:

`The service principal named <appName> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.`

Aby rozwiązać ten problem, użyj środowiska zgody administratora, aby utworzyć nazwę główną usługi aplikacji klienta w dzierżawie, lub utwórz ją ręcznie.  To wymaganie zapewnia, że dzierżawca udzielił uprawnienia aplikacji do działania w ramach dzierżawy.

#### <a name="example-request"></a>Przykładowe żądanie

`https://login.microsoftonline.com/contoso.com/oauth2/authorize?resource=https://gateway.contoso.com/api&response_type=token&client_id=14c88eee-b3e2-4bb0-9233-f5e3053b3a28&...` W tym przykładzie dzierżawca zasobów (Urząd) to contoso.com, aplikacja zasobów jest aplikacją jednodostępną o nazwie `gateway.contoso.com/api` dzierżawcy contoso, a aplikacja kliencka `14c88eee-b3e2-4bb0-9233-f5e3053b3a28` .  Jeśli aplikacja kliencka ma nazwę główną usługi w ramach Contoso.com, to żądanie może być kontynuowane.  W przeciwnym razie żądanie zakończy się niepowodzeniem z błędem powyżej.

Jeśli jednak aplikacja bramy contoso była aplikacją z wieloma dzierżawcami, żądanie będzie kontynuowane bez względu na to, czy aplikacja kliencka ma nazwę główną usługi w Contoso.com.

### <a name="redirect-uris-can-now-contain-query-string-parameters"></a>Identyfikatory URI przekierowania mogą teraz zawierać parametry ciągu zapytania

**Data wprowadzenia**: 22 lipca 2019

**Wpływ na punkty końcowe**: 1.0 i v 2.0

**Wpływ na protokół**: wszystkie przepływy

Na [RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2)aplikacje usługi Azure AD mogą teraz rejestrować i używać identyfikatorów URI przekierowania (odpowiedź) przy użyciu statycznych parametrów zapytania (takich jak `https://contoso.com/oauth2?idp=microsoft` ) dla żądań uwierzytelniania OAuth 2,0.  Dynamiczne identyfikatory URI przekierowania są nadal zabronione, ponieważ stanowią zagrożenie bezpieczeństwa i nie mogą być używane do zachowywania informacji o stanie w ramach żądania uwierzytelniania — w tym celu należy użyć `state` parametru.

Statyczny parametr zapytania jest uzależniony od ciągu dla identyfikatorów URI przekierowania, takich jak jakakolwiek inna część identyfikatora URI przekierowania — Jeśli nie jest zarejestrowany żaden ciąg pasujący do redirect_uri z zdekodowanym identyfikatorem URI, żądanie zostanie odrzucone.  Jeśli identyfikator URI zostanie znaleziony w rejestracji aplikacji, cały ciąg zostanie użyty do przekierowania użytkownika, łącznie z parametrem zapytania statycznego.

Należy pamiętać, że w tej chwili (koniec lipca 2019) środowisko rejestracji aplikacji w Azure Portal nadal blokuje parametry zapytania.  Można jednak ręcznie edytować manifest aplikacji w celu dodania parametrów zapytania i przetestowania go w aplikacji.


## <a name="march-2019"></a>Marzec 2019 r.

### <a name="looping-clients-will-be-interrupted"></a>Zapętlenie klientów zostanie przerwane

**Data wprowadzenia**: 25 marca 2019

**Wpływ na punkty końcowe**: 1.0 i v 2.0

**Wpływ na protokół**: wszystkie przepływy

Aplikacje klienckie mogą czasami niedziałać, wydając setki tego samego żądania logowania w krótkim czasie.  Te żądania mogą się nie powieść, ale wszystkie mają wpływ na słabe środowisko użytkownika i zwiększone obciążenia dla dostawcy tożsamości, zwiększając opóźnienia dla wszystkich użytkowników i redukując dostępność dostawcy tożsamości.  Te aplikacje działają poza granicami normalnego użycia i należy je zaktualizować, aby działały prawidłowo.

Klienci, którzy wydają zduplikowane żądania wiele razy, będą mogli wysłać `invalid_grant` błąd: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` .

Większość klientów nie będzie musiała zmienić zachowania, aby uniknąć tego błędu.  Ten błąd będzie miał wpływ tylko na niepoprawnie skonfigurowanych klientów (bez buforowania tokenu lub tych, które są już wyświetlane).  Klienci są śledzoni lokalnie na poszczególnych wystąpieniach (za pośrednictwem pliku cookie) na następujących czynnikach:

* Wskazówka użytkownika, jeśli istnieje

* Żądane zakresy lub zasoby

* Identyfikator klienta

* Identyfikator URI przekierowania

* Typ i tryb odpowiedzi

Aplikacje wykonujące wiele żądań (15 +) w krótkim czasie (5 minut) otrzymają komunikat `invalid_grant` o błędzie z informacją, że są zapętlenia.  Żądane tokeny mają wystarczająco długie okresy istnienia (co najmniej 10 minut, domyślnie 60 minut), więc powtarzające się żądania w tym okresie są zbędne.

Wszystkie aplikacje powinny być obsługiwane `invalid_grant` przez wyświetlanie interakcyjnego monitu, a nie w trybie dyskretnym.  Aby uniknąć tego błędu, klienci powinni upewnić się, że prawidłowo buforują otrzymywane tokeny.


## <a name="october-2018"></a>Październik 2018 r.

### <a name="authorization-codes-can-no-longer-be-reused"></a>Nie można już ponownie używać kodów autoryzacji

**Data wprowadzenia**: 15 listopada 2018

**Wpływ na punkty końcowe**: 1.0 i v 2.0

**Wpływ na protokół**: [przepływ kodu](v2-oauth2-auth-code-flow.md)

Od 15 listopada 2018 usługa Azure AD nie będzie akceptować poprzednio używanych kodów uwierzytelniania dla aplikacji. Ta zmiana zabezpieczeń pomaga w przeniesieniu usługi Azure AD zgodnie ze specyfikacją OAuth i wymuszać zarówno punkty końcowe V1, jak i v2.

Jeśli aplikacja ponownie używa kodów autoryzacji w celu uzyskania tokenów dla wielu zasobów, zalecamy użycie kodu w celu uzyskania tokenu odświeżania, a następnie użycie tego tokenu odświeżania do uzyskania dodatkowych tokenów dla innych zasobów. Kody autoryzacji mogą być używane tylko raz, ale tokeny odświeżania można wielokrotnie używać w wielu zasobach. W przypadku każdej nowej aplikacji próbującej ponownie użyć kodu uwierzytelniania podczas przepływu kodu OAuth Wystąpił błąd invalid_grant.

Aby uzyskać więcej informacji na temat tokenów odświeżania, zobacz [odświeżanie tokenów dostępu](v2-oauth2-auth-code-flow.md#refresh-the-access-token).  W przypadku używania biblioteki ADAL lub MSAL jest to obsługiwane przez bibliotekę — Zastąp drugie wystąpienie elementu "AcquireTokenByAuthorizationCodeAsync" elementem "AcquireTokenSilentAsync".

## <a name="may-2018"></a>Maj 2018 r.

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>Nie można używać tokenów identyfikatora dla przepływu OBO

**Data**: 1 maja, 2018

**Wpływ na punkty końcowe**: 1.0 i v 2.0

**Wpływ na protokoły**: niejawny przepływ i [w imieniu przepływu](v2-oauth2-on-behalf-of-flow.md)

Od 1 maja 2018 id_tokens nie może być używany jako potwierdzenie w przepływie OBO dla nowych aplikacji. Tokeny dostępu należy zamiast tego używać w celu zabezpieczania interfejsów API, nawet między klientem a warstwą środkową tej samej aplikacji. Aplikacje zarejestrowane przed 1 maja 2018 będą nadal działać i mogą być dostępne do wymiany id_tokens dla tokenu dostępu. Jednak ten wzorzec nie jest uważany za najlepsze rozwiązanie.

Aby obejść tę zmianę, można wykonać następujące czynności:

1. Utwórz internetowy interfejs API dla aplikacji z co najmniej jednym zakresem. Ten jawny punkt wejścia umożliwi dokładniejszą kontrolę i bezpieczeństwo.
1. W manifeście aplikacji, w [Azure Portal](https://portal.azure.com) lub [portalu rejestracji aplikacji](https://apps.dev.microsoft.com), upewnij się, że aplikacja może wystawiać tokeny dostępu za pośrednictwem niejawnego przepływu. Jest to kontrolowane przez `oauth2AllowImplicitFlow` klucz.
1. Gdy aplikacja kliencka żąda id_token za pośrednictwem programu `response_type=id_token` , należy również zażądać tokenu dostępu ( `response_type=token` ) dla internetowego interfejsu API utworzonego powyżej. W tym przypadku, podczas korzystania z punktu końcowego v 2.0, `scope` parametr powinien wyglądać podobnie do `api://GUID/SCOPE` . W punkcie końcowym v 1.0 `resource` parametr powinien być identyfikatorem URI aplikacji interfejsu API sieci Web.
1. Przekaż ten token dostępu do warstwy środkowej zamiast id_token.
