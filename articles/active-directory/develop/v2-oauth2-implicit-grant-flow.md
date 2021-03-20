---
title: Niejawny przepływ uwierzytelniania OAuth 2,0 — platforma tożsamości firmy Microsoft | Azure
description: Zabezpiecz aplikacje jednostronicowe przy użyciu niejawnego przepływu platformy tożsamości firmy Microsoft.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: f3598c6f072d09d7e427db66dcfbf8721b92a3a1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99226492"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Microsoft Identity platform i niejawny przepływ dotacji

Platforma tożsamości firmy Microsoft obsługuje niejawny przepływ uwierzytelniania OAuth 2,0 zgodnie z opisem w [specyfikacji oauth 2,0](https://tools.ietf.org/html/rfc6749#section-4.2). Definiowanie cech charakterystycznych niejawnego przyznania polega na tym, że tokeny (tokeny identyfikatorów lub tokeny dostępu) są zwracane bezpośrednio z punktu końcowego/Authorize zamiast punktu końcowego/token. Jest to często używane w ramach [przepływu kodu autoryzacji](v2-oauth2-auth-code-flow.md), w którym nazywa się "przepływ hybrydowy" — pobranie tokenu identyfikatora w żądaniu/Authorize oraz kodu autoryzacji.

[!INCLUDE [suggest-msal-from-protocols](includes/suggest-msal-from-protocols.md)]

## <a name="prefer-the-auth-code-flow"></a>Preferuj przepływ kodu uwierzytelniania

Przy użyciu planów dla [plików cookie innych firm, które mają zostać usunięte z przeglądarek](reference-third-party-cookies-spas.md), **niejawny przepływ uprawnień nie jest już odpowiednią metodą uwierzytelniania**.  [Dyskretne funkcje logowania jednokrotnego](#getting-access-tokens-silently-in-the-background) przepływu niejawnego nie działają bez plików cookie innych firm, powodując przerwanie działania aplikacji podczas próby uzyskania nowego tokenu. Zdecydowanie zalecamy, aby wszystkie nowe aplikacje korzystały z [przepływu kodu autoryzacji](v2-oauth2-auth-code-flow.md) , który obsługuje aplikacje jednostronicowe zamiast niejawnego przepływu oraz że [istniejące aplikacje jednostronicowe zaczynają migrować do przepływu kodu autoryzacji](migrate-spa-implicit-to-auth-code.md) .

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Odpowiednie scenariusze dla niejawnego udzielenia OAuth2

Niejawne przyznanie jest niezawodne tylko dla początkowej, interaktywnej części przepływu logowania, w przypadku których brak [plików cookie innych](reference-third-party-cookies-spas.md) firm nie ma wpływu na aplikację. To ograniczenie oznacza, że należy używać go wyłącznie w ramach przepływu hybrydowego, w którym aplikacja żąda kodu, a także tokenu z punktu końcowego autoryzacji. Dzięki temu aplikacja otrzymuje kod, który może zostać zrealizowany dla tokenu odświeżania, dzięki czemu sesja logowania aplikacji pozostaje ważna z upływem czasu.

## <a name="protocol-diagram"></a>Diagram protokołu

Na poniższym diagramie przedstawiono, jak wygląda cały niejawny przepływ logowania, a kolejne sekcje opisują każdy krok bardziej szczegółowo.

![Diagram przedstawiający niejawny przepływ logowania](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>Wyślij żądanie logowania

Aby najpierw podpisać użytkownika w aplikacji, możesz wysłać żądanie uwierzytelniania [OpenID Connect Connect](v2-protocols-oidc.md) i uzyskać dostęp do usługi `id_token` Microsoft Identity platform.

> [!IMPORTANT]
> Aby pomyślnie zażądać tokenu identyfikatora i/lub tokenu dostępu, Rejestracja aplikacji na stronie [Azure Portal-rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) musi mieć odpowiedni niejawny przepływ dotacji, wybierając **tokeny identyfikatorów** i **tokeny dostępu** w sekcji **niejawne uprawnienia i przepływy hybrydowe** . Jeśli nie jest włączona, `unsupported_response` zostanie zwrócony błąd: `The provided value for the input parameter 'response_type' is not allowed for this client. Expected value is 'code'`

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid
&response_mode=fragment
&state=12345
&nonce=678910
```

> [!TIP]
> Aby przetestować Logowanie przy użyciu przepływu niejawnego, kliknij pozycję <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank"> https://login.microsoftonline.com/common/oauth2/v2.0/authorize.. .</a> Po zalogowaniu przeglądarka powinna zostać przekierowana do programu `https://localhost/myapp/` przy użyciu `id_token` na pasku adresu.
>

| Parametr | Typ | Opis |
| --- | --- | --- |
| `tenant` | wymagane |`{tenant}`Wartość w ścieżce żądania może służyć do kontrolowania, kto może zalogować się do aplikacji. Dozwolone wartości to `common` , `organizations` , `consumers` i identyfikator dzierżawy. Aby uzyskać więcej informacji, zobacz temat [podstawowe informacje o protokole](active-directory-v2-protocols.md#endpoints). |
| `client_id` | wymagane | Identyfikator aplikacji (klienta), którą strona [Rejestracje aplikacji Azure Portala](https://go.microsoft.com/fwlink/?linkid=2083908) została przypisana do aplikacji. |
| `response_type` | wymagane |Musi zawierać `id_token` do logowania za OpenID Connect Connect. Może również zawierać response_type `token` . Użycie w `token` tym miejscu umożliwi aplikacji otrzymywanie tokenu dostępu od razu od autoryzowanego punktu końcowego bez konieczności podawania drugiego żądania do autoryzowanego punktu końcowego. Jeśli używasz `token` response_type, `scope` parametr musi zawierać zakres wskazujący zasób, dla którego ma zostać wystawiony token (na przykład user. read on Microsoft Graph). Może również zawierać `code` `token` kod autoryzacji do użycia w [przepływie kodu autoryzacji](v2-oauth2-auth-code-flow.md). Ta id_token + odpowiedź na kod jest czasami nazywana przepływem hybrydowym.  |
| `redirect_uri` | zalecane |Redirect_uri aplikacji, w której odpowiedzi uwierzytelniania mogą być wysyłane i odbierane przez aplikację. Musi dokładnie pasować do jednego z redirect_uris zarejestrowanego w portalu, z wyjątkiem tego, że musi on być zakodowany w adresie URL. |
| `scope` | wymagane |Rozdzielana spacjami lista [zakresów](v2-permissions-and-consent.md). W przypadku programu OpenID Connect Connect (id_tokens) musi zawierać zakres `openid` , który tłumaczy uprawnienie "Logowanie do Ciebie" w interfejsie użytkownika wyrażania zgody. Opcjonalnie możesz również uwzględnić zakresy i, aby `email` uzyskać `profile` dostęp do dodatkowych danych użytkownika. W przypadku żądania zgody na różne zasoby można również uwzględnić inne zakresy w żądaniu. |
| `response_mode` | optional |Określa metodę, która ma zostać użyta do wysłania zwróconego tokenu z powrotem do aplikacji. Domyślnie kwerenda dotyczy tylko tokenu dostępu, ale fragmentu, jeśli żądanie zawiera id_token. |
| `state` | zalecane |Wartość uwzględniona w żądaniu, która również zostanie zwrócona w odpowiedzi tokenu. Może to być ciąg dowolnej zawartości. Losowo wygenerowana unikatowa wartość jest zwykle używana w celu [zapobiegania atakom na fałszerstwo żądań między witrynami](https://tools.ietf.org/html/rfc6749#section-10.12). Ten stan jest również używany do kodowania informacji o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelnienia, takiego jak strona lub widok. |
| `nonce` | wymagane |Wartość uwzględniona w żądaniu wygenerowanym przez aplikację, która zostanie uwzględniona w uzyskanym id_token jako jako żądanie. Następnie aplikacja może zweryfikować tę wartość, aby zmniejszyć ataki metodą powtórzeń tokenu. Wartość jest zazwyczaj losowo losowym, unikatowym ciągiem, który może służyć do identyfikowania pochodzenia żądania. Wymagane tylko wtedy, gdy zażądano id_token. |
| `prompt` | optional |Wskazuje typ interakcji użytkownika, która jest wymagana. Jedyne prawidłowe wartości w tym momencie to "login'", "none", "select_account" i "zgody". `prompt=login` wymusić użytkownikowi wprowadzanie poświadczeń na to żądanie, negację logowania jednokrotnego. `prompt=none` jest przeciwieństwem do siebie, upewnia się, że użytkownik nie jest wyświetlany z żadnym interaktywnym monitem. Jeśli żądanie nie może zostać zakończone dyskretnie przy użyciu logowania jednokrotnego, platforma tożsamości firmy Microsoft zwróci błąd. `prompt=select_account` wysyła użytkownika do selektora konta, w którym zostaną wyświetlone wszystkie konta zapamiętane w sesji. `prompt=consent` spowoduje wyzwolenie okna dialogowego zgody na uwierzytelnianie OAuth po zalogowaniu się użytkownika, z prośbą o udzielenie uprawnień do aplikacji. |
| `login_hint`  |optional |Może służyć do wstępnego wypełniania pola Nazwa użytkownika/adres e-mail na stronie logowania dla użytkownika, jeśli znana jest jego nazwa użytkownika przed czasem. Często aplikacje będą używać tego parametru podczas ponownego uwierzytelniania, ponieważ już wyodrębnili nazwę użytkownika z poprzedniego logowania przy użyciu tego `preferred_username` żądania.|
| `domain_hint` | optional |W przypadku pominięcia zostanie pominięty proces odnajdywania na podstawie poczty e-mail, który użytkownik przejdzie na stronie logowania, co prowadzi do nieco bardziej usprawnionego środowiska użytkownika. Ten parametr jest często używany w przypadku aplikacji biznesowych, które działają w ramach pojedynczej dzierżawy, gdzie podają nazwę domeny w ramach danej dzierżawy, przekazując użytkownika do dostawcy federacyjnego dla tej dzierżawy.  Należy zauważyć, że ta Wskazówka uniemożliwia Gościom zalogowanie się do tej aplikacji i ograniczenie użycia poświadczeń w chmurze, takich jak FIDO.  |

W tym momencie użytkownik zostanie poproszony o wprowadzenie poświadczeń i zakończenie uwierzytelniania. Platforma tożsamości firmy Microsoft sprawdzi również, czy użytkownik wyraził zgodę na uprawnienia wskazane w `scope` parametrze zapytania. Jeśli użytkownik wyraził zgodę na **żadne** z tych uprawnień, skontaktuje się z użytkownikiem, aby zalecić wymagane uprawnienia. Aby uzyskać więcej informacji, zobacz [uprawnienia, zgoda i aplikacje z wieloma dzierżawcami](v2-permissions-and-consent.md).

Po uwierzytelnieniu i udzieleniu zgody użytkownik platformy tożsamości firmy Microsoft zwróci odpowiedź do aplikacji we wskazanym miejscu `redirect_uri` przy użyciu metody określonej w `response_mode` parametrze.

#### <a name="successful-response"></a>Pomyślna odpowiedź

Pomyślna odpowiedź przy użyciu `response_mode=fragment` i wygląda następująco `response_type=id_token+code` (z podziałami wierszy na potrzeby czytelności):

```HTTP
GET https://localhost/myapp/#
code=0.AgAAktYV-sfpYESnQynylW_UKZmH-C9y_G1A
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Parametr | Opis |
| --- | --- |
| `code` | Uwzględnione, jeśli `response_type` zawiera `code` . Jest to kod autoryzacji odpowiedni do użycia w [przepływie kodu autoryzacji](v2-oauth2-auth-code-flow.md).  |
| `access_token` |Uwzględnione, jeśli `response_type` zawiera `token` . Token dostępu, którego żądała aplikacja. Token dostępu nie powinien być zdekodowany lub w inny sposób sprawdzany, powinien być traktowany jako ciąg nieprzezroczysty. |
| `token_type` |Uwzględnione, jeśli `response_type` zawiera `token` . Zawsze będzie `Bearer` . |
| `expires_in`|Uwzględnione, jeśli `response_type` zawiera `token` . Wskazuje liczbę sekund ważności tokenu na potrzeby buforowania. |
| `scope` |Uwzględnione, jeśli `response_type` zawiera `token` . Wskazuje zakresy, dla których access_token będzie prawidłowy. Nie może zawierać wszystkich żądanych zakresów, jeśli nie mają zastosowania do użytkownika (w przypadku zakresów tylko w usłudze Azure AD, które są wymagane w przypadku logowania za pomocą konta osobistego). |
| `id_token` | Podpisany token sieci Web JSON (JWT). Aplikacja może zdekodować segmenty tego tokenu, aby zażądać informacji o użytkowniku, który się zalogował. Aplikacja może buforować wartości i wyświetlać je, ale nie należy polegać na nich dla żadnych ograniczeń autoryzacji lub zabezpieczeń. Aby uzyskać więcej informacji na temat id_tokens, zobacz [`id_token reference`](id-tokens.md) . <br> **Uwaga:** Dostępne tylko wtedy `openid` , gdy zakres został żądany i `response_type` uwzględniony `id_tokens` . |
| `state` |Jeśli w żądaniu zostanie uwzględniony parametr stanu, ta sama wartość powinna pojawić się w odpowiedzi. Aplikacja powinna sprawdzić, czy wartości stanu w żądaniu i odpowiedzi są identyczne. |

#### <a name="error-response"></a>Odpowiedź na błąd

Odpowiedzi na błędy mogą być również wysyłane do, aby `redirect_uri` aplikacja mogła je odpowiednio obsłużyć:

```HTTP
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parametr | Opis |
| --- | --- |
| `error` |Ciąg kodu błędu, który może służyć do klasyfikowania typów błędów, które występują i mogą być używane do reagowania na błędy. |
| `error_description` |Konkretny komunikat o błędzie, który może ułatwić deweloperom zidentyfikowanie głównej przyczyny błędu uwierzytelniania. |

## <a name="getting-access-tokens-silently-in-the-background"></a>Uzyskiwanie tokenów dostępu w trybie dyskretnym w tle

> [!Important]
> Ta część niejawnego przepływu jest mało prawdopodobne, aby można było korzystać z aplikacji, ponieważ jest ona używana w różnych przeglądarkach z powodu [usuwania plików cookie innych](reference-third-party-cookies-spas.md)firm.  Mimo że obecnie działa w przeglądarkach opartych na chromie, które nie znajdują się w incognito, deweloperzy należy rozważyć przy użyciu tej części przepływu. W przeglądarkach, które nie obsługują plików cookie innych firm, zostanie wyświetlony komunikat o błędzie informujący, że żaden użytkownik nie jest zalogowany, ponieważ pliki cookie sesji strony logowania zostały usunięte przez przeglądarkę. 

Teraz, gdy użytkownik został zarejestrowany w aplikacji jednostronicowej, można w trybie dyskretnym uzyskać tokeny dostępu do wywoływania interfejsów API sieci Web zabezpieczonych przez platformę tożsamości firmy Microsoft, takich jak [Microsoft Graph](https://developer.microsoft.com/graph). Nawet jeśli token został już odebrany przy użyciu `token` response_type, można użyć tej metody, aby uzyskać tokeny dla dodatkowych zasobów bez konieczności przekierowania użytkownika w celu ponownego zalogowania.

W normalnym przepływie OpenID Connect Connect/OAuth należy to zrobić, wysyłając żądanie do punktu końcowego platformy tożsamości firmy Microsoft `/token` . Możesz wprowadzić żądanie w ukrytym elemencie iframe, aby uzyskać nowe tokeny dla innych interfejsów API sieci Web:

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&response_mode=fragment
&state=12345
&nonce=678910
&prompt=none
&login_hint=myuser@mycompany.com
```

Aby uzyskać szczegółowe informacje na temat parametrów zapytania w adresie URL, zobacz [Wysyłanie żądania logowania](#send-the-sign-in-request).

> [!TIP]
> Spróbuj skopiować & wklejając poniższe żądanie do karty przeglądarki! (Nie zapomnij zastąpić `login_hint` wartości poprawną wartością dla użytkownika)
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint={your-username}`
>
> Należy pamiętać, że ta funkcja będzie działała nawet w przeglądarkach bez obsługi plików cookie innych firm, ponieważ wprowadzasz ją bezpośrednio do paska przeglądarki, w przeciwieństwie do otwierania go w elemencie iframe. 

Za pomocą `prompt=none` tego parametru żądanie zakończy się pomyślnie lub natychmiast zakończy się niepowodzeniem i wróci do aplikacji. Odpowiedź zostanie wysłana do aplikacji w wskazanym miejscu `redirect_uri` przy użyciu metody określonej w `response_mode` parametrze.

#### <a name="successful-response"></a>Pomyślna odpowiedź

Pomyślna odpowiedź przy użyciu `response_mode=fragment` wygląda następująco:

```HTTP
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fdirectory.read
```

| Parametr | Opis |
| --- | --- |
| `access_token` |Uwzględnione, jeśli `response_type` zawiera `token` . Token dostępu, którego żądał aplikacja, w tym przypadku Microsoft Graph. Token dostępu nie powinien być zdekodowany lub w inny sposób sprawdzany, powinien być traktowany jako ciąg nieprzezroczysty. |
| `token_type` | Zawsze będzie `Bearer` . |
| `expires_in` | Wskazuje liczbę sekund ważności tokenu na potrzeby buforowania. |
| `scope` | Wskazuje zakresy, dla których access_token będzie prawidłowy. Nie może zawierać wszystkich żądanych zakresów, jeśli nie mają zastosowania do użytkownika (w przypadku zakresów tylko w usłudze Azure AD, które są wymagane w przypadku logowania za pomocą konta osobistego). |
| `id_token` | Podpisany token sieci Web JSON (JWT). Uwzględnione, jeśli `response_type` zawiera `id_token` . Aplikacja może zdekodować segmenty tego tokenu, aby zażądać informacji o użytkowniku, który się zalogował. Aplikacja może buforować wartości i wyświetlać je, ale nie należy polegać na nich dla żadnych ograniczeń autoryzacji lub zabezpieczeń. Aby uzyskać więcej informacji na temat id_tokens, zobacz [ `id_token` odwołanie](id-tokens.md). <br> **Uwaga:** Dostępne tylko wtedy, gdy `openid` żądany został zakres. |
| `state` |Jeśli w żądaniu zostanie uwzględniony parametr stanu, ta sama wartość powinna pojawić się w odpowiedzi. Aplikacja powinna sprawdzić, czy wartości stanu w żądaniu i odpowiedzi są identyczne. |

#### <a name="error-response"></a>Odpowiedź na błąd

Odpowiedzi na błędy mogą być również wysyłane do, aby `redirect_uri` aplikacja mogła je odpowiednio obsłużyć. W przypadku `prompt=none` , oczekiwany błąd to:

```HTTP
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parametr | Opis |
| --- | --- |
| `error` |Ciąg kodu błędu, który może służyć do klasyfikowania typów błędów, które występują i mogą być używane do reagowania na błędy. |
| `error_description` |Konkretny komunikat o błędzie, który może ułatwić deweloperom zidentyfikowanie głównej przyczyny błędu uwierzytelniania. |

Jeśli ten błąd wystąpi w żądaniu iframe, użytkownik musi interaktywnie zalogować się ponownie, aby pobrać nowy token. Możesz obsłużyć ten przypadek w dowolny sposób, który ma sens dla aplikacji.

## <a name="refreshing-tokens"></a>Odświeżanie tokenów

Niejawne przyznanie nie zapewnia tokenów odświeżania. Oba `id_token` elementy s i `access_token` s wygasną po krótkim czasie, więc należy przygotować aplikację do okresowego odświeżania tych tokenów. Aby odświeżyć każdy typ tokenu, można wykonać te same ukryte żądanie iframe z powyżej przy użyciu `prompt=none` parametru, aby kontrolować zachowanie platformy tożsamości. Jeśli chcesz otrzymywać nowe `id_token` , pamiętaj, aby użyć `id_token` w `response_type` i `scope=openid` , a także `nonce` parametru.

W przeglądarkach, które nie obsługują plików cookie innych firm, spowoduje to błąd wskazujący, że żaden użytkownik nie jest zalogowany. 

## <a name="send-a-sign-out-request"></a>Wyślij żądanie wylogowania

OpenID Connect Connect `end_session_endpoint` umożliwia aplikacji wysyłanie żądania do platformy tożsamości firmy Microsoft w celu zakończenia sesji użytkownika i czyszczenie plików cookie ustawionych przez platformę tożsamości firmy Microsoft. Aby w pełni podpisać użytkownika z aplikacji sieci Web, aplikacja powinna zakończyć własną sesję użytkownika (zazwyczaj przez wyczyszczenie pamięci podręcznej tokenów lub usunięcie plików cookie), a następnie przekierować przeglądarkę do:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Parametr | Typ | Opis |
| --- | --- | --- |
| `tenant` |wymagane |`{tenant}`Wartość w ścieżce żądania może służyć do kontrolowania, kto może zalogować się do aplikacji. Dozwolone wartości to `common` , `organizations` , `consumers` i identyfikator dzierżawy. Aby uzyskać więcej informacji, zobacz temat [podstawowe informacje o protokole](active-directory-v2-protocols.md#endpoints). |
| `post_logout_redirect_uri` | zalecane | Adres URL, do którego użytkownik powinien zostać zwrócony po zakończeniu wylogowywania. Ta wartość musi być zgodna z jednym z identyfikatorów URI przekierowania zarejestrowanych dla aplikacji. Jeśli ta wartość nie jest uwzględniona, użytkownik zobaczy komunikat ogólny na platformie tożsamości firmy Microsoft. |

## <a name="next-steps"></a>Następne kroki

* Aby rozpocząć kodowanie, przejdź do [przykładów MSAL js](sample-v2-code.md) .
* Zapoznaj się z [przepływem kodu autoryzacji](v2-oauth2-auth-code-flow.md) jako nowszą, lepszą alternatywą dla niejawnego udzielenia. 
