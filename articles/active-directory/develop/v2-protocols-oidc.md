---
title: Protokół OpenID Connect — platforma tożsamości firmy Microsoft | Azure
description: Tworzenie aplikacji sieci web przy użyciu implementacji platformy tożsamości firmy Microsoft protokołu uwierzytelniania OpenID Connect.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 161f97dc99ce5ce16d7c40126b95a769c4b79621
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868336"
---
# <a name="microsoft-identity-platform-and-openid-connect-protocol"></a>Platforma tożsamości firmy Microsoft i protokół OpenID Connect

OpenID Connect to protokół uwierzytelniania zbudowany na OAuth 2.0, którego można użyć do bezpiecznego logowania użytkownika do aplikacji sieci web. Korzystając z implementacji openid connect platformy tożsamości firmy Microsoft, można dodać dostęp do logowania i interfejsu API do aplikacji opartych na sieci Web. W tym artykule pokazano, jak to zrobić niezależnie od języka i opisano sposób wysyłania i odbierania wiadomości HTTP bez korzystania z bibliotek open source firmy Microsoft.

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) rozszerza protokół *autoryzacji* OAuth 2.0 do użycia jako protokół *uwierzytelniania,* dzięki czemu można zrobić logowanie jednokrotne przy użyciu OAuth. OpenID Connect wprowadza pojęcie *tokenu identyfikatora*, który jest tokenem zabezpieczającym, który umożliwia klientowi weryfikowanie tożsamości użytkownika. Token identyfikatora pobiera również podstawowe informacje o profilu użytkownika. Ponieważ OpenID Connect rozszerza OAuth 2.0, aplikacje mogą bezpiecznie uzyskać *tokeny dostępu,* które mogą być używane do uzyskiwania dostępu do zasobów zabezpieczonych przez [serwer autoryzacji.](active-directory-v2-protocols.md#the-basics) Punkt końcowy platformy tożsamości firmy Microsoft umożliwia również aplikacjom innych firm, które są zarejestrowane w usłudze Azure AD, wystawianie tokenów dostępu dla zabezpieczonych zasobów, takich jak internetowe interfejsy API. Aby uzyskać więcej informacji na temat konfigurowania aplikacji do wystawiania tokenów dostępu, zobacz [Jak zarejestrować aplikację w punkcie końcowym platformy tożsamości firmy Microsoft](quickstart-register-app.md). Zaleca się używanie OpenID Connect, jeśli budujesz [aplikację sieci web,](v2-app-types.md#web-apps) która jest hostowana na serwerze i uzyskiwać dostęp za pośrednictwem przeglądarki.

## <a name="protocol-diagram-sign-in"></a>Diagram protokołu: Logowanie

Najbardziej podstawowy przepływ logowania ma kroki pokazane na następnym diagramie. Każdy krok jest szczegółowo opisany w tym artykule.

![Protokół OpenID Connect: Logowanie](./media/v2-protocols-oidc/convergence-scenarios-webapp.svg)

## <a name="fetch-the-openid-connect-metadata-document"></a>Pobieranie dokumentu metadanych OpenID Connect

OpenID Connect opisuje dokument metadanych, który zawiera większość informacji wymaganych dla aplikacji do logowania. Obejmuje to informacje, takie jak adresy URL do użycia i lokalizacja publicznych kluczy podpisywania usługi. W przypadku punktu końcowego platformy tożsamości firmy Microsoft jest to dokument metadanych OpenID Connect, którego należy użyć:

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
```

> [!TIP]
> Wypróbuj! Kliknij, [https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) aby `common` wyświetlić konfigurację dzierżawców.

Może `{tenant}` przyjąć jedną z czterech wartości:

| Wartość | Opis |
| --- | --- |
| `common` |Użytkownicy korzystający zarówno z osobistego konta Microsoft, jak i konta służbowego z usługi Azure AD mogą logować się do aplikacji. |
| `organizations` |Tylko użytkownicy z kontami służbowymi lub szkolnymi z usługi Azure AD mogą logować się do aplikacji. |
| `consumers` |Tylko użytkownicy posiadający osobiste konto Microsoft mogą zalogować się do aplikacji. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` lub `contoso.onmicrosoft.com` | Tylko użytkownicy z określonej dzierżawy usługi Azure AD (niezależnie od tego, czy są członkami katalogu z kontem służbowym lub szkolnym, czy są gośćmi w katalogu za pomocą osobistego konta Microsoft) mogą zalogować się do aplikacji. Można użyć przyjaznej nazwy domeny dzierżawy usługi Azure AD lub identyfikatora GUID dzierżawy. Można również użyć dzierżawy `9188040d-6c67-4c5b-b112-36a304b66dad`konsumenta, `consumers` zamiast dzierżawy.  |

Metadane to prosty dokument notacji obiektu JavaScript (JSON). Zobacz poniższy fragment kodu na przykład. Zawartość fragmentu kodu jest w pełni opisana w [specyfikacji OpenID Connect](https://openid.net/specs/openid-connect-discovery-1_0.html#rfc.section.4.2).

```json
{
  "authorization_endpoint": "https:\/\/login.microsoftonline.com\/{tenant}\/oauth2\/v2.0\/authorize",
  "token_endpoint": "https:\/\/login.microsoftonline.com\/{tenant}\/oauth2\/v2.0\/token",
  "token_endpoint_auth_methods_supported": [
    "client_secret_post",
    "private_key_jwt"
  ],
  "jwks_uri": "https:\/\/login.microsoftonline.com\/{tenant}\/discovery\/v2.0\/keys",

  ...

}
```

Jeśli aplikacja ma niestandardowe klucze podpisywania w wyniku korzystania z `appid` funkcji [mapowania oświadczeń,](active-directory-claims-mapping.md) należy dołączyć parametr kwerendy zawierający identyfikator aplikacji, aby uzyskać `jwks_uri` wskazanie informacji o kluczu podpisywania aplikacji. Na przykład: `https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` `jwks_uri` zawiera `https://login.microsoftonline.com/{tenant}/discovery/v2.0/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`a z .

Zazwyczaj ten dokument metadanych służy do konfigurowania biblioteki OpenID Connect lub SDK; biblioteka będzie używać metadanych do wykonywania swojej pracy. Jeśli jednak nie używasz wstępnie utworzonej biblioteki OpenID Connect, możesz wykonać kroki opisane w dalszej części tego artykułu, aby zalogować się do aplikacji sieci web przy użyciu punktu końcowego platformy tożsamości firmy Microsoft.

## <a name="send-the-sign-in-request"></a>Wyślij żądanie logowania

Gdy aplikacja sieci web musi uwierzytelnić użytkownika, może `/authorize` skierować użytkownika do punktu końcowego. To żądanie jest podobne do pierwszego etapu [przepływu kodu autoryzacyjnego OAuth 2.0,](v2-oauth2-auth-code-flow.md)z tymi ważnymi różnicami:

* Żądanie musi zawierać `openid` zakres `scope` w parametrze.
* Parametr `response_type` musi `id_token`zawierać .
* Żądanie musi zawierać `nonce` parametr.

> [!IMPORTANT]
> Aby pomyślnie zażądać tokenu identyfikatora z punktu końcowego /authorization, rejestracja aplikacji w [portalu rejestracji](https://portal.azure.com) musi mieć niejawną `oauth2AllowIdTokenImplicitFlow` funkcję udzielania id_tokens na karcie Uwierzytelnianie (która ustawia flagę w [manifeście aplikacji](reference-app-manifest.md) `true`). Jeśli nie jest włączona, `unsupported_response` zostanie zwrócony błąd: "Podana wartość parametru wejściowego 'response_type' nie jest dozwolona dla tego klienta. Oczekiwaną wartością jest 'code'"

Przykład:

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=form_post
&scope=openid
&state=12345
&nonce=678910
```

> [!TIP]
> Kliknij poniższe łącze, aby wykonać to żądanie. Po zalogowaniu się przeglądarka zostanie `https://localhost/myapp/`przekierowana do tokenu identyfikatora na pasku adresu. Należy pamiętać, że `response_mode=fragment` to żądanie używa (tylko w celach demonstracyjnych). Zalecamy użycie `response_mode=form_post`programu .
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| Parametr | Warunek | Opis |
| --- | --- | --- |
| `tenant` | Wymagany | Można użyć `{tenant}` wartości w ścieżce żądania, aby kontrolować, kto może zalogować się do aplikacji. Dozwolone wartości to `common` `organizations`, `consumers`, i identyfikatory dzierżawy. Aby uzyskać więcej informacji, zobacz [podstawy protokołu](active-directory-v2-protocols.md#endpoints). |
| `client_id` | Wymagany | **Identyfikator aplikacji (klienta),** który usługa Azure portal — środowisko [rejestracji aplikacji przypisane](https://go.microsoft.com/fwlink/?linkid=2083908) do aplikacji. |
| `response_type` | Wymagany | Musi `id_token` zawierać dla openid connect logowania. Może również zawierać `response_type` inne wartości, takie jak `code`. |
| `redirect_uri` | Zalecane | Identyfikator URI przekierowania aplikacji, w którym aplikacja może wysyłać i odbierać odpowiedzi na uwierzytelnianie. Musi dokładnie odpowiadać jednemu z identyfikatorów URI przekierowania zarejestrowanych w portalu, z tą różnicą, że musi być zakodowany adres URL. Jeśli nie ma, punkt końcowy wybierze jeden zarejestrowany redirect_uri losowo, aby wysłać użytkownika z powrotem do. |
| `scope` | Wymagany | Oddzielona spacja lista zakresów. W przypadku openid connect musi `openid`zawierać zakres, który przekłada się na uprawnienie "Zaloguj się" w interfejsie użytkownika zgody. Możesz również uwzględnić inne zakresy w tym żądaniu zgody. |
| `nonce` | Wymagany | Wartość uwzględniona w żądaniu, wygenerowana przez aplikację, która zostanie uwzględniona w wynikowej wartości id_token jako oświadczenie. Aplikacja może zweryfikować tę wartość, aby ograniczyć ataki powtarzania tokenów. Wartość zazwyczaj jest losowo, unikatowy ciąg, który może służyć do identyfikowania pochodzenia żądania. |
| `response_mode` | Zalecane | Określa metodę, która powinna być używana do wysyłania wynikowego kodu autoryzacji z powrotem do aplikacji. Możliwe wartości to `form_post` i `fragment`. W przypadku aplikacji sieci `response_mode=form_post`web zaleca się użycie programu , aby zapewnić najbezpieczniejszy transfer tokenów do aplikacji. |
| `state` | Zalecane | Wartość zawarta w żądaniu, która również zostanie zwrócona w odpowiedzi tokenu. Może to być ciąg dowolnej zawartości. Losowo generowana unikatowa wartość jest zwykle używana do [zapobiegania atakom fałszerskości żądań między witrynami.](https://tools.ietf.org/html/rfc6749#section-10.12) Stan jest również używany do kodowania informacji o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelniania, takich jak strona lub widok, na którego znajdował się użytkownik. |
| `prompt` | Optional (Opcjonalność) | Wskazuje typ interakcji użytkownika, który jest wymagany. Jedynymi prawidłowymi wartościami `login` `none`w `consent`tej chwili są , i . Oświadczenie `prompt=login` wymusza na użytkowniku wprowadzenie poświadczeń w tym żądaniu, co neguje logowanie jednokrotne. Twierdzenie `prompt=none` jest przeciwne. To oświadczenie gwarantuje, że użytkownik nie jest wyświetlany z żadnym interaktywnym monitem. Jeśli nie można ukończyć żądania w trybie dyskretnym za pomocą logowania jednokrotnego, punkt końcowy platformy tożsamości firmy Microsoft zwraca błąd. Oświadczenie `prompt=consent` wyzwala okno dialogowe zgody OAuth po zalogowaniu się użytkownika. Okno dialogowe prosi użytkownika o przyznanie uprawnień do aplikacji. |
| `login_hint` | Optional (Opcjonalność) | Ten parametr służy do wstępnego wypełniania pola nazwy użytkownika i adresu e-mail strony logowania dla użytkownika, jeśli znasz nazwę użytkownika z wyprzedzeniem. Często aplikacje używają tego parametru podczas ponownego uwierzytelniania, po wyodrębnieniu już nazwy `preferred_username` użytkownika z wcześniejszego logowania przy użyciu oświadczenia. |
| `domain_hint` | Optional (Opcjonalność) | Sfera użytkownika w katalogu federatywnym.  Pomija to proces odnajdywania opartego na wiadomości e-mail, który użytkownik przechodzi na stronie logowania, aby uzyskać nieco bardziej usprawnione środowisko użytkownika. W przypadku dzierżawców, które są sfederowane za pośrednictwem katalogu lokalnego, takiego jak usługi AD FS, często powoduje to bezproblemowe logowanie z powodu istniejącej sesji logowania. |

W tym momencie użytkownik jest monitowany o wprowadzenie poświadczeń i zakończenie uwierzytelniania. Punkt końcowy platformy tożsamości firmy Microsoft sprawdza, czy użytkownik wyraził zgodę na uprawnienia wskazane w parametrze `scope` zapytania. Jeśli użytkownik nie wyraził zgody na którekolwiek z tych uprawnień, punkt końcowy platformy tożsamości firmy Microsoft monituje użytkownika o wyrażenie zgody na wymagane uprawnienia. Więcej informacji o [uprawnieniach, zgodach i aplikacjach z wieloma dzierżawami](v2-permissions-and-consent.md)można przeczytać.

Po uwierzytelnieniu i udzieleniu zgody przez użytkownika punkt końcowy platformy tożsamości firmy Microsoft zwraca odpowiedź do aplikacji `response_mode` przy wskazanym identyfikatorze URI przekierowania przy użyciu metody określonej w parametrze.

### <a name="successful-response"></a>Skuteczna reakcja

Pomyślna odpowiedź podczas `response_mode=form_post` korzystania wygląda następująco:

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parametr | Opis |
| --- | --- |
| `id_token` | Token identyfikatora, o który prosiła aplikacja. Za pomocą `id_token` parametru można zweryfikować tożsamość użytkownika i rozpocząć sesję z użytkownikiem. Aby uzyskać więcej informacji na temat tokenów identyfikatorów i ich zawartości, zobacz [ `id_tokens` odwołanie](id-tokens.md). |
| `state` | Jeśli `state` parametr jest uwzględniony w żądaniu, ta sama wartość powinna pojawić się w odpowiedzi. Aplikacja powinna sprawdzić, czy wartości stanu w żądaniu i odpowiedzi są identyczne. |

### <a name="error-response"></a>Odpowiedź na błąd

Odpowiedzi na błędy mogą być również wysyłane do identyfikatora URI przekierowania, dzięki czemu aplikacja może je obsługiwać. Odpowiedź na błąd wygląda następująco:

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parametr | Opis |
| --- | --- |
| `error` | Ciąg kodu błędu, który służy do klasyfikowania typów błędów, które występują i reagować na błędy. |
| `error_description` | Określony komunikat o błędzie, który może pomóc w zidentyfikowaniu głównej przyczyny błędu uwierzytelniania. |

### <a name="error-codes-for-authorization-endpoint-errors"></a>Kody błędów dla błędów punktu końcowego autoryzacji

W poniższej tabeli opisano kody `error` błędów, które mogą być zwracane w parametrze odpowiedzi na błąd:

| Kod błędu | Opis | Akcja klienta |
| --- | --- | --- |
| `invalid_request` | Błąd protokołu, taki jak brakujący, wymagany parametr. |Napraw i ponownie prześlij żądanie. Jest to błąd rozwoju, który zazwyczaj jest przechwytyny podczas początkowego testowania. |
| `unauthorized_client` | Aplikacja kliencka nie może zażądać kodu autoryzacji. |Zwykle dzieje się tak, gdy aplikacja kliencka nie jest zarejestrowana w usłudze Azure AD lub nie jest dodawana do dzierżawy usługi Azure AD użytkownika. Aplikacja może monitować użytkownika o instrukcje, aby zainstalować aplikację i dodać ją do usługi Azure AD. |
| `access_denied` | Właściciel zasobu odmówił zgody. |Aplikacja kliencka może powiadomić użytkownika, że nie może kontynuować, chyba że użytkownik wyrazi na to zgodę. |
| `unsupported_response_type` |Serwer autoryzacji nie obsługuje typu odpowiedzi w żądaniu. |Napraw i ponownie prześlij żądanie. Jest to błąd rozwoju, który zazwyczaj jest przechwytyny podczas początkowego testowania. |
| `server_error` | Serwer napotkał nieoczekiwany błąd. |Ponów próbę żądania. Błędy te mogą wynikać z warunków tymczasowych. Aplikacja kliencka może wyjaśnić użytkownikowi, że jego odpowiedź jest opóźniona z powodu błędu tymczasowego. |
| `temporarily_unavailable` | Serwer jest tymczasowo zbyt zajęty, aby obsłużyć żądanie. |Ponów próbę żądania. Aplikacja kliencka może wyjaśnić użytkownikowi, że jego odpowiedź jest opóźniona z powodu stanu tymczasowego. |
| `invalid_resource` | Zasób docelowy jest nieprawidłowy, ponieważ nie istnieje, usługa Azure AD nie może go znaleźć lub nie jest poprawnie skonfigurowany. |Oznacza to, że zasób, jeśli istnieje, nie został skonfigurowany w dzierżawie. Aplikacja może monitować użytkownika o instrukcje dotyczące instalowania aplikacji i dodawania jej do usługi Azure AD. |

## <a name="validate-the-id-token"></a>Sprawdzanie poprawności tokenu identyfikatora

Samo odebranie id_token nie wystarczy do uwierzytelnienia użytkownika; należy sprawdzić poprawność podpisu id_token i zweryfikować oświadczenia w tokenie zgodnie z wymaganiami aplikacji. Punkt końcowy platformy tożsamości firmy Microsoft używa [tokenów JSON Web Tokens (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) i kryptografii klucza publicznego do podpisywania tokenów i sprawdzania, czy są one prawidłowe.

Można wybrać, aby `id_token` sprawdzić poprawność w kodzie klienta, `id_token` ale powszechną praktyką jest wysłanie do serwera wewnętrznej bazy danych i wykonać sprawdzanie poprawności tam. Po sprawdzeniu poprawności podpisu id_token, istnieje kilka oświadczeń, które będziesz musiał zweryfikować. Zobacz [ `id_token` odwołanie, aby](id-tokens.md) uzyskać więcej informacji, w tym [sprawdzanie poprawności tokenów](id-tokens.md#validating-an-id_token) i [ważne informacje dotyczące podpisywania narzucania kluczy](active-directory-signing-key-rollover.md). Zaleca się korzystanie z biblioteki do analizowania i sprawdzania poprawności tokenów — jest co najmniej jeden dostępny dla większości języków i platform.

Można również sprawdzić poprawność dodatkowych oświadczeń w zależności od scenariusza. Niektóre typowe weryfikacje obejmują:

* Upewnienie się, że użytkownik/organizacja zarejestrowała się w aplikacji.
* Zapewnienie użytkownikowi odpowiedniej autoryzacji/uprawnień
* Zapewnienie pewnej siły uwierzytelniania, takich jak uwierzytelnianie wieloskładnikowe.

Po sprawdzeniu poprawności id_token można rozpocząć sesję z użytkownikiem i użyć oświadczeń w id_token, aby uzyskać informacje o użytkowniku w aplikacji. Informacje te mogą być używane do wyświetlania, zapisów, personalizacji itp.

## <a name="send-a-sign-out-request"></a>Wysyłanie żądania wylogowywania

Jeśli chcesz wylogować użytkownika z aplikacji, nie wystarczy wyczyścić pliki cookie aplikacji lub w inny sposób zakończyć sesję użytkownika. Należy również przekierować użytkownika do punktu końcowego platformy tożsamości firmy Microsoft, aby się wylogować. Jeśli nie zrobisz tego, użytkownik ponownie uwierzytelnia się do aplikacji bez wprowadzania ich poświadczenia ponownie, ponieważ będą one miały prawidłową sesję logowania jednokrotnego z punktu końcowego platformy tożsamości firmy Microsoft.

Możesz przekierować użytkownika `end_session_endpoint` do wymienionych w dokumencie metadanych OpenID Connect:

```HTTP
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parametr | Warunek | Opis |
| ----------------------- | ------------------------------- | ------------ |
| `post_logout_redirect_uri` | Zalecane | Adres URL, do który użytkownik jest przekierowywał po pomyślnym wylogowaniu. Jeśli parametr nie jest uwzględniony, użytkownik jest wyświetlany ogólny komunikat, który jest generowany przez punkt końcowy platformy tożsamości firmy Microsoft. Ten adres URL musi być zgodny z jednym z identyfikatorów URI przekierowania zarejestrowanych dla aplikacji w portalu rejestracji aplikacji. |

## <a name="single-sign-out"></a>Wylogowanie jednokrotne

Po przekierowaniu użytkownika `end_session_endpoint`do punktu końcowego platformy tożsamości firmy Microsoft czyści sesję użytkownika z przeglądarki. Jednak użytkownik może nadal być zalogowany do innych aplikacji, które używają kont Microsoft do uwierzytelniania. Aby umożliwić tym aplikacjom jednoczesne wylogowywanie użytkownika, punkt końcowy platformy `LogoutUrl` tożsamości firmy Microsoft wysyła żądanie HTTP GET do zarejestrowanych wszystkich aplikacji, do których użytkownik jest aktualnie zalogowany. Aplikacje muszą odpowiedzieć na to żądanie, czyszcząc dowolną `200` sesję, która identyfikuje użytkownika i zwracając odpowiedź. Jeśli chcesz obsługiwać wylogowywania w aplikacji, `LogoutUrl` należy zaimplementować takie w kodzie aplikacji. Można ustawić `LogoutUrl` z portalu rejestracji aplikacji.

## <a name="protocol-diagram-access-token-acquisition"></a>Diagram protokołu: Pozyskiwanie tokenów dostępu

Wiele aplikacji sieci web musi nie tylko zalogować użytkownika, ale także uzyskać dostęp do usługi sieci web w imieniu użytkownika za pomocą OAuth. W tym scenariuszu łączy OpenID Connect do uwierzytelniania użytkownika, jednocześnie uzyskując kod autoryzacji, którego można użyć, aby uzyskać tokeny dostępu, jeśli używasz przepływu kodu autoryzacji OAuth.

Pełny przepływ logowania i pozyskiwania tokenów OpenID Connect wygląda podobnie do następnego diagramu. Opisujemy każdy krok szczegółowo w następnych sekcjach artykułu.

![Protokół OpenID Connect: Pozyskiwanie tokenów](./media/v2-protocols-oidc/convergence-scenarios-webapp-webapi.svg)

## <a name="get-access-tokens"></a>Uzyskaj tokeny dostępu
Aby uzyskać tokeny dostępu, zmodyfikuj żądanie logowania:

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application ID
&response_type=id_token%20code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered redirect URI, URL encoded
&response_mode=form_post                              // 'form_post' or 'fragment'
&scope=openid%20                                      // Include both 'openid' and scopes that your app needs
offline_access%20
https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

> [!TIP]
> Kliknij poniższe łącze, aby wykonać to żądanie. Po zalogowaniu się przeglądarka zostanie `https://localhost/myapp/`przekierowana do tokenu identyfikatora i kodu na pasku adresu. Należy pamiętać, że `response_mode=fragment` to żądanie używa tylko do celów demonstracyjnych. Zalecamy użycie `response_mode=form_post`programu .
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token%20code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=fragment&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

Dołączając zakresy uprawnień w `response_type=id_token code`żądaniu i używając punktu końcowego platformy tożsamości firmy Microsoft, aby `scope` użytkownik wyraził zgodę na uprawnienia wskazane w parametrze zapytania. Zwraca kod autoryzacji do aplikacji w celu wymiany tokenu dostępu.

### <a name="successful-response"></a>Skuteczna reakcja

Pomyślna odpowiedź `response_mode=form_post` z wykorzystaniem wygląda następująco:

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parametr | Opis |
| --- | --- |
| `id_token` | Token identyfikatora, o który prosiła aplikacja. Token identyfikatora służy do weryfikacji tożsamości użytkownika i rozpoczęcia sesji z użytkownikiem. Więcej informacji na temat tokenów identyfikatorów i ich zawartości znajdziesz w [ `id_tokens` odwołaniu.](id-tokens.md) |
| `code` | Kod autoryzacji, o który prosiła aplikacja. Aplikacja może użyć kodu autoryzacji, aby zażądać tokenu dostępu dla zasobu docelowego. Kod autoryzacji jest krótkotrwały. Zazwyczaj kod autoryzacji wygasa za około 10 minut. |
| `state` | Jeśli parametr stanu znajduje się w żądaniu, ta sama wartość powinna pojawić się w odpowiedzi. Aplikacja powinna sprawdzić, czy wartości stanu w żądaniu i odpowiedzi są identyczne. |

### <a name="error-response"></a>Odpowiedź na błąd

Odpowiedzi na błędy mogą być również wysyłane do identyfikatora URI przekierowania, dzięki czemu aplikacja może obsługiwać je odpowiednio. Odpowiedź na błąd wygląda następująco:

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parametr | Opis |
| --- | --- |
| `error` | Ciąg kodu błędu, który służy do klasyfikowania typów błędów, które występują i reagować na błędy. |
| `error_description` | Określony komunikat o błędzie, który może pomóc w zidentyfikowaniu głównej przyczyny błędu uwierzytelniania. |

Aby uzyskać opis możliwych kodów błędów i zalecanych odpowiedzi klientów, zobacz [Kody błędów dla błędów punktów końcowych autoryzacji](#error-codes-for-authorization-endpoint-errors).

Jeśli masz kod autoryzacji i token identyfikatora, można zalogować użytkownika i uzyskać tokeny dostępu w ich imieniu. Aby zalogować się do użytkownika, należy sprawdzić poprawność tokenu identyfikatora [dokładnie tak, jak opisano.](id-tokens.md#validating-an-id_token) Aby uzyskać tokeny dostępu, wykonaj kroki opisane w [dokumentacji przepływu kodu OAuth](v2-oauth2-auth-code-flow.md#request-an-access-token).
