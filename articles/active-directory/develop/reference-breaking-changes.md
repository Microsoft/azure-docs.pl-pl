---
title: Odwołanie do zmian z podziałem usługi Azure Active Directory
description: Dowiedz się więcej o zmianach wprowadzonych w protokołach usługi Azure AD, które mogą mieć wpływ na aplikację.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 3/13/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: a60b927f7239818b582ffcd85ddb4b7d69594482
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535965"
---
# <a name="whats-new-for-authentication"></a>Co nowego w uwierzytelnianiu?

>Otrzymuuj powiadomienia o aktualizacjach tej strony. Wystarczy dodać [ten adres URL](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us) do czytnika kanałów RSS.

System uwierzytelniania zmienia i dodaje funkcje na bieżąco w celu poprawy bezpieczeństwa i zgodności z normami. Aby być na bieżąco z najnowszymi wydarzeniami, w tym artykule znajdziesz informacje o następujących szczegółach:

- Najnowsze funkcje
- Znane problemy
- Zmiany protokołu
- Funkcje uznane za przestarzałe

> [!TIP]
> Ta strona jest regularnie aktualizowana, więc odwiedzaj często. O ile nie zaznaczono inaczej, zmiany te są wprowadzane tylko dla nowo zarejestrowanych wniosków.

## <a name="upcoming-changes"></a>Nadchodzące zmiany

Żaden zaplanowano w tej chwili.  Poniżej znajdują się zmiany, które są w produkcji lub nadchodzą.

## <a name="march-2020"></a>Marzec 2020 r.

### <a name="user-passwords-will-be-restricted-to-256-characters"></a>Hasła użytkowników będą ograniczone do 256 znaków.

**Data wejścia w życie**: 13 marca 2020 r.

**Wpływ na punkty końcowe:** zarówno w wersji 1.0, jak i v2.0

**Wpływ na protokół:** Wszystkie przepływy użytkownika.

Użytkownicy z hasłami dłuższymi niż 256 znaków, którzy logują się bezpośrednio do usługi Azure AD (w przeciwieństwie do federacyjnego protokołu IDP, takiego jak ADFS), nie będą mogli zalogować się od 13 marca 2020 r. i zamiast tego zostaną poproszeni o zresetowanie hasła.  Administratorzy mogą otrzymywać prośby o pomoc w zresetowaniu hasła użytkowników.

Błąd w logach logowania będzie AADSTS 50052: InvalidPasswordExceedsMaxLength

Komunikat:`The password entered exceeds the maximum length of 256. Please reach out to your admin to reset the password.`

Korygowania:

Użytkownik nie może się zalogować, ponieważ jego hasło przekracza dozwoloną maksymalną długość. Powinni skontaktować się ze swoim administratorem, aby zresetować hasło. Jeśli funkcja SSPR jest włączona dla ich dzierżawy, mogą zresetować swoje hasło, wykonując link "Zapomniałeś hasła".



## <a name="february-2020"></a>Luty 2020 r.

### <a name="empty-fragments-will-be-appended-to-every-http-redirect-from-the-login-endpoint"></a>Puste fragmenty zostaną dołączone do każdego przekierowania HTTP z punktu końcowego logowania.

**Data wejścia w życie**: 8 lutego 2020 r.

**Wpływ na punkty końcowe:** zarówno w wersji 1.0, jak i v2.0

Protokół wpływ : **Przepływy**OAuth i OIDC, które używają response_type = query - obejmuje to [przepływ kodu autoryzacji](v2-oauth2-auth-code-flow.md) w niektórych przypadkach i [niejawny przepływ](v2-oauth2-implicit-grant-flow.md).

Gdy odpowiedź uwierzytelniania jest wysyłana z login.microsoftonline.com do aplikacji za pośrednictwem przekierowania HTTP, usługa dołączy pusty fragment do adresu URL odpowiedzi.  Zapobiega to atakom przekierowania klasy, zapewniając, że przeglądarka usuwa istniejący fragment w żądaniu uwierzytelniania.  Żadna aplikacja nie powinna mieć zależności od tego zachowania.


## <a name="august-2019"></a>Sierpień 2019 r.

### <a name="post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored"></a>Semantyka formularza POST będzie egzekwowana bardziej rygorystycznie - spacje i cudzysłowy będą ignorowane

**Data wejścia w życie**: 2 września 2019 r.

**Wpływ na punkty końcowe:** zarówno w wersji 1.0, jak i v2.0

**Protokół, którego dotyczy wpływ:** Gdziekolwiek post jest używany[(poświadczenia klienta,](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow) [wykorzystanie kodu autoryzacji,](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow) [ROPC,](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth-ropc) [OBO](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)i [odświeżanie tokenu wykupu)](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow#refresh-the-access-token)

Począwszy od tygodnia 9/2, żądania uwierzytelniania, które używają metody POST zostaną sprawdzone przy użyciu bardziej rygorystycznych standardów HTTP.  W szczególności spacje i cudzysłowy (") nie będą już usuwane z wartości formularza żądania. Te zmiany nie powinny przerywać istniejących klientów i zapewni, że żądania wysyłane do usługi Azure AD są niezawodnie obsługiwane za każdym razem. W przyszłości (patrz wyżej) planujemy dodatkowo odrzucić zduplikowane parametry i zignorować BOM w żądaniach.

Przykład:

Dziś `?e=    "f"&g=h` jest analizowany identycznie `?e=f&g=h` jak `e`  ==  `f`- tak .  Dzięki tej zmianie będzie teraz analizowana `e`  ==  `    "f"` tak, że - jest mało prawdopodobne, aby był to prawidłowy argument, a żądanie zakończy się niepowodzeniem.


## <a name="july-2019"></a>Lipiec 2019 r.

### <a name="app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant"></a>Tokeny tylko do aplikacji dla aplikacji z jedną dzierżawą są wystawiane tylko wtedy, gdy aplikacja kliencka istnieje w dzierżawie zasobów

**Data wejścia w życie**: 26 lipca 2019 r.

**Wpływ na punkty końcowe:** zarówno [w wersji 1.0,](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow) jak i [v2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)

**Wpływ na protokół:** [Poświadczenia klienta (tokeny tylko do aplikacji)](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)

Zmiana zabezpieczeń została włączona 26 lipca, która zmienia sposób wystawiania tokenów tylko do aplikacji (za pośrednictwem dotacji poświadczeń klienta). Wcześniej aplikacje mogły uzyskać tokeny do wywoływania dowolnej innej aplikacji, niezależnie od obecności w dzierżawie lub ról, na które udzielono zgody dla tej aplikacji.  To zachowanie zostało zaktualizowane, tak aby dla zasobów (czasami nazywanych interfejsami API sieci web) ustawionych na jedną dzierżawę (domyślnie), aplikacja kliencka musi istnieć w dzierżawie zasobów.  Należy zauważyć, że istniejąca zgoda między klientem a interfejsem API nadal nie jest `roles` wymagana, a aplikacje powinny nadal wykonywać własne kontrole autoryzacji, aby upewnić się, że oświadczenie jest obecne i zawiera oczekiwaną wartość dla interfejsu API.

Komunikat o błędzie dla tego scenariusza stanowi obecnie:

`The service principal named <appName> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.`

Aby rozwiązać ten problem, należy użyć środowiska Zgody administratora, aby utworzyć jednostkę usługi aplikacji klienckiej w dzierżawie lub utworzyć ją ręcznie.  To wymaganie zapewnia, że dzierżawca udzielił aplikacji uprawnienia do działania w ramach dzierżawy.

#### <a name="example-request"></a>Przykładowe żądanie

`https://login.microsoftonline.com/contoso.com/oauth2/authorize?resource=https://gateway.contoso.com/api&response_type=token&client_id=14c88eee-b3e2-4bb0-9233-f5e3053b3a28&...`W tym przykładzie dzierżawy zasobów (urząd) jest contoso.com, aplikacja zasobów `gateway.contoso.com/api` jest aplikacją pojedynczej dzierżawy `14c88eee-b3e2-4bb0-9233-f5e3053b3a28`wywoływane dla dzierżawy Contoso, a aplikacja kliencka jest .  Jeśli aplikacja kliencka ma jednostkę usługi w ciągu Contoso.com, to żądanie może być kontynuowane.  Jeśli jednak nie, żądanie zakończy się niepowodzeniem z błędem powyżej.

Jeśli contoso aplikacji bramy były aplikacje wielu dzierżawców, jednak żądanie będzie kontynuowane niezależnie od aplikacji klienckiej o jednostki usługi w ciągu Contoso.com.

### <a name="redirect-uris-can-now-contain-query-string-parameters"></a>Przekierowanie identyfikatorów URI może teraz zawierać parametry ciągu zapytania

**Data wejścia w życie**: 22 lipca 2019 r.

**Wpływ na punkty końcowe:** zarówno w wersji 1.0, jak i v2.0

**Wpływ protokołu**: Wszystkie przepływy

Zgodnie z [RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2)aplikacje usługi Azure AD można teraz zarejestrować i używać `https://contoso.com/oauth2?idp=microsoft`przekierowania (odpowiedzi) identyfikatory URI ze statycznymi parametrami kwerendy (takich jak) dla żądań OAuth 2.0.  Dynamiczne identyfikatory URI przekierowania są nadal zabronione, ponieważ stanowią zagrożenie bezpieczeństwa i nie można ich `state` używać do przechowywania informacji o stanie w żądaniu uwierzytelniania — w tym celu należy użyć tego parametru.

Parametr zapytania statycznego podlega dopasowywaniu ciągów do przekierowania identyfikatorów URI, jak każda inna część identyfikatora URI przekierowania — jeśli nie jest zarejestrowany żaden ciąg odpowiadający redirect_uri zdekodowanym identyfikatorem URI, żądanie zostanie odrzucone.  Jeśli identyfikator URI zostanie znaleziony w rejestracji aplikacji, cały ciąg będzie używany do przekierowania użytkownika, w tym parametru zapytania statycznego.

Należy zauważyć, że w tej chwili (koniec lipca 2019 r.) środowisko użytkownika rejestracji aplikacji w witrynie Azure portal nadal blokuje parametry zapytania.  Jednak manifest aplikacji można edytować ręcznie, aby dodać parametry kwerendy i przetestować to w aplikacji.


## <a name="march-2019"></a>Marzec 2019 r.

### <a name="looping-clients-will-be-interrupted"></a>Zapętlanie klientów zostanie przerwane

**Data wejścia w życie**: 25 marca 2019 r.

**Wpływ na punkty końcowe:** zarówno w wersji 1.0, jak i v2.0

**Wpływ protokołu**: Wszystkie przepływy

Aplikacje klienckie mogą czasami zachowywać się nieprawidłowo, wydając setki tego samego żądania logowania w krótkim okresie czasu.  Te żądania mogą lub nie mogą zakończyć się powodzeniem, ale wszystkie one przyczyniają się do słabego środowiska użytkownika i zwiększone obciążenia dla IDP, zwiększenie opóźnienia dla wszystkich użytkowników i zmniejszenie dostępności idp.  Te aplikacje działają poza granicami normalnego użytkowania i powinny być aktualizowane, aby zachowywać się poprawnie.

Klienci, którzy wielokrotnie wystawiają `invalid_grant` zduplikowane żądania, otrzymają błąd: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request`.

Większość klientów nie będzie musiał zmienić zachowanie, aby uniknąć tego błędu.  Ten błąd będzie miał wpływ tylko na nieprawidłowo skonfigurowanych klientów (tych bez buforowania tokenów lub tych, którzy już wykazują pętle monitów).  Klienci są śledzone na podstawie wystąpienia lokalnie (za pośrednictwem plików cookie) na następujące czynniki:

* Podpowiedź użytkownika, jeśli istnieje

* Żądane zakresy lub zasób

* Identyfikator klienta

* Identyfikator URI przekierowania

* Typ i tryb odpowiedzi

Aplikacje składające wiele żądań (15+) w krótkim czasie `invalid_grant` (5 minut) otrzymają błąd wyjaśniający, że są one zapętlone.  Tokeny wymagane mają wystarczająco długowieczne okresy istnienia (minimum 10 minut, domyślnie 60 minut), więc powtarzające się żądania w tym okresie czasu są niepotrzebne.

Wszystkie aplikacje `invalid_grant` powinny obsługiwać, wyświetlając interaktywny monit, a nie dyskretnie żądając tokenu.  Aby uniknąć tego błędu, klienci powinni upewnić się, że są poprawnie buforowania tokenów, które otrzymują.


## <a name="october-2018"></a>Październik 2018 r.

### <a name="authorization-codes-can-no-longer-be-reused"></a>Kody autoryzacyjne nie mogą być już ponownie

**Data wejścia w życie**: 15 listopada 2018 r.

**Wpływ na punkty końcowe:** zarówno w wersji 1.0, jak i v2.0

**Wpływ protokołu**: [Przepływ kodu](v2-oauth2-auth-code-flow.md)

Począwszy od 15 listopada 2018 r. usługa Azure AD przestanie akceptować wcześniej używane kody uwierzytelniania dla aplikacji. Ta zmiana zabezpieczeń pomaga dostosować usługę Azure AD do specyfikacji OAuth i będzie wymuszana zarówno w punktach końcowych w wersji 1, jak i w wersji 2.

Jeśli aplikacja ponownie używa kodów autoryzacji, aby uzyskać tokeny dla wielu zasobów, zaleca się użycie kodu, aby uzyskać token odświeżania, a następnie użyć tego tokenu odświeżania, aby uzyskać dodatkowe tokeny dla innych zasobów. Kody autoryzacji można używać tylko raz, ale tokeny odświeżania mogą być używane wiele razy w wielu zasobach. Każda nowa aplikacja, która próbuje ponownie użyć kodu uwierzytelniania podczas przepływu kodu OAuth otrzyma błąd invalid_grant.

Aby uzyskać więcej informacji na temat tokenów odświeżania, zobacz [Odświeżanie tokenów dostępu](v2-oauth2-auth-code-flow.md#refresh-the-access-token).  Jeśli używasz ADAL lub MSAL, jest to obsługiwane przez bibliotekę — zastąp drugie wystąpienie "AcquireTokenByAuthorizationCodeAsync" na "AcquireTokenSilentAsync".

## <a name="may-2018"></a>Maj 2018 r.

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>Tokeny identyfikatorów nie mogą być używane dla przepływu OBO

**Data**: 1 maja 2018 r.

**Wpływ na punkty końcowe:** zarówno w wersji 1.0, jak i v2.0

**Protokoły, na które wpływ miało wpływ:** Przepływ niejawny i [przepływ w imieniu](v2-oauth2-on-behalf-of-flow.md)

Po 1 maja 2018 r. nie można id_tokens służyć jako potwierdzenie w przepływie OBO dla nowych aplikacji. Tokeny dostępu powinny być używane zamiast do zabezpieczania interfejsów API, nawet między warstwą klienta i warstwy środkowej tej samej aplikacji. Aplikacje zarejestrowane przed 1 maja 2018 r. będą nadal działać i będą mogły wymieniać id_tokens token dostępu; jednak ten wzorzec nie jest uważany za najlepszą praktykę.

Aby obejść tę zmianę, można wykonać następujące czynności:

1. Utwórz internetowy interfejs API dla aplikacji z co najmniej jednym zakresem. Ten jawny punkt wejścia pozwoli na lepszą kontrolę i zabezpieczenia.
1. W manifeście aplikacji w [witrynie Azure portal](https://portal.azure.com) lub portalu rejestracji [aplikacji](https://apps.dev.microsoft.com)upewnij się, że aplikacja może wystawiać tokeny dostępu za pośrednictwem przepływu niejawnego. Jest to kontrolowane `oauth2AllowImplicitFlow` przez klucz.
1. Gdy aplikacja kliencka żąda id_token `response_type=id_token`za pośrednictwem ,`response_type=token`zażądaj również tokenu dostępu ( ) dla utworzonego powyżej internetowego interfejsu API. Tak więc podczas korzystania z punktu końcowego w wersji 2.0 `scope` parametr powinien wyglądać podobnie do `api://GUID/SCOPE`. W punkcie końcowym w wersji 1.0 `resource` parametr powinien być identyfikatorem URI aplikacji interfejsu API sieci web.
1. Przekaż ten token dostępu do warstwy środkowej zamiast id_token.
