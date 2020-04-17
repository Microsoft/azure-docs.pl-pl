---
title: Przepływ kodu autoryzacji OAuth - Platforma tożsamości firmy Microsoft | Azure
description: Tworzenie aplikacji sieci web przy użyciu implementacji platformy tożsamości firmy Microsoft protokołu uwierzytelniania OAuth 2.0.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: ac630c4901c126ed883adbdc7efb03f36372e6ff
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535880"
---
# <a name="microsoft-identity-platform-and-oauth-20-authorization-code-flow"></a>Platforma tożsamości firmy Microsoft i przepływ kodu autoryzacji OAuth 2.0

Udzielanie kodu autoryzacji OAuth 2.0 może służyć w aplikacjach zainstalowanych na urządzeniu w celu uzyskania dostępu do chronionych zasobów, takich jak internetowe interfejsy API. Korzystając z implementacji platformy tożsamości firmy Microsoft OAuth 2.0, można dodać dostęp logowania i interfejsu API do aplikacji mobilnych i klasycznych. Ten przewodnik jest niezależny od języka i opisuje sposób wysyłania i odbierania wiadomości HTTP bez korzystania z bibliotek [uwierzytelniania typu azure typu open source.](reference-v2-libraries.md)

W tym artykule opisano sposób programowania bezpośrednio względem protokołu w aplikacji.  Jeśli to możliwe, zaleca się użycie obsługiwanych bibliotek uwierzytelniania firmy Microsoft (MSAL) zamiast tego do [uzyskiwania tokenów i wywoływania zabezpieczonych interfejsów API sieci Web](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Zapoznaj się również z [przykładowymi aplikacjami korzystającymi z programu MSAL](sample-v2-code.md).

> [!NOTE]
> Nie wszystkie scenariusze usługi Azure Active Directory & funkcje są obsługiwane przez punkt końcowy platformy tożsamości firmy Microsoft. Aby ustalić, czy należy używać punktu końcowego platformy tożsamości firmy Microsoft, przeczytaj o [ograniczeniach platformy tożsamości firmy Microsoft](active-directory-v2-limitations.md).

Przepływ kodu autoryzacji OAuth 2.0 jest opisany w [sekcji 4.1 specyfikacji OAuth 2.0](https://tools.ietf.org/html/rfc6749). Służy do uwierzytelniania i autoryzacji w większości typów aplikacji, w tym [w aplikacjach internetowych](v2-app-types.md#web-apps) i [natywnie zainstalowanych aplikacjach.](v2-app-types.md#mobile-and-native-apps) Przepływ umożliwia aplikacjom bezpieczne pozyskiwanie access_tokens, które mogą służyć do uzyskiwania dostępu do zasobów zabezpieczonych przez punkt końcowy platformy tożsamości firmy Microsoft.

## <a name="protocol-diagram"></a>Diagram protokołu

Na wysokim poziomie cały przepływ uwierzytelniania dla aplikacji natywnej/mobilnej wygląda nieco tak:

![Przepływ kodu OAuth Auth](./media/v2-oauth2-auth-code-flow/convergence-scenarios-native.svg)

## <a name="request-an-authorization-code"></a>Poproś o kod autoryzacji

Przepływ kodu autoryzacji rozpoczyna się od klienta `/authorize` kierującego użytkownika do punktu końcowego. W tym żądaniu klient `openid` `offline_access`żąda `https://graph.microsoft.com/mail.read `, i uprawnienia od użytkownika.  Niektóre uprawnienia są ograniczone przez administratora, na przykład zapisywanie `Directory.ReadWrite.All`danych w katalogu organizacji przy użyciu programu . Jeśli aplikacja żąda dostępu do jednego z tych uprawnień od użytkownika organizacji, użytkownik otrzymuje komunikat o błędzie informujący, że nie jest upoważniony do wyrażenia zgody na uprawnienia aplikacji. Aby zażądać dostępu do zakresów z ograniczeniami administratora, należy poprosić o nie bezpośrednio administratora firmy.  Aby uzyskać więcej informacji, zobacz [uprawnienia z ograniczeniami administratora](v2-permissions-and-consent.md#admin-restricted-permissions).

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345
```

> [!TIP]
> Kliknij poniższy link, aby wykonać to żądanie! Po zalogowaniu się przeglądarka powinna `https://localhost/myapp/` zostać `code` przekierowana do a na pasku adresu.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| Parametr    | Wymagane/opcjonalne | Opis |
|--------------|-------------|--------------|
| `tenant`    | wymagany    | Wartość `{tenant}` w ścieżce żądania może służyć do kontrolowania, kto może zalogować się do aplikacji. Dozwolone wartości to `common` `organizations`, `consumers`, i identyfikatory dzierżawy. Aby uzyskać więcej informacji, zobacz [podstawy protokołu](active-directory-v2-protocols.md#endpoints).  |
| `client_id`   | wymagany    | **Identyfikator aplikacji (klienta),** który usługa Azure portal — środowisko [rejestracji aplikacji przypisane](https://go.microsoft.com/fwlink/?linkid=2083908) do aplikacji.  |
| `response_type` | wymagany    | Musi `code` zawierać dla przepływu kodu autoryzacji.       |
| `redirect_uri`  | wymagany | Redirect_uri aplikacji, w której aplikacja może wysyłać i odbierać odpowiedzi na uwierzytelnianie. Musi dokładnie odpowiadać jednej z redirect_uris zarejestrowanej w portalu, z tą różnicą, że musi być zakodowany adres URL. W przypadku aplikacji mobilnych & natywnych `https://login.microsoftonline.com/common/oauth2/nativeclient`należy użyć domyślnej wartości programu .   |
| `scope`  | wymagany    | Oddzielona spacja lista [zakresów,](v2-permissions-and-consent.md) na które użytkownik ma wyrazić zgodę.  W `/authorize` przypadku części żądania może to obejmować wiele zasobów, dzięki czemu aplikacja może uzyskać zgodę na wiele interfejsów API sieci Web, które chcesz wywołać. |
| `response_mode`   | zalecane | Określa metodę, która powinna służyć do wysyłania tokenu wynikowego z powrotem do aplikacji. Może być jedną z następujących czynności:<br/><br/>- `query`<br/>- `fragment`<br/>- `form_post`<br/><br/>`query`udostępnia kod jako parametr ciągu zapytania w identyfikatorze URI przekierowania. Jeśli żądasz tokenu identyfikatora przy użyciu przepływu niejawnego, nie możesz użyć `query` tego, co określono w [specyfikacji OpenID](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations). Jeśli żądasz tylko kodu, możesz użyć `query` `fragment`, `form_post`, lub . `form_post`wykonuje post zawierający kod do identyfikatora URI przekierowania. Aby uzyskać więcej informacji, zobacz [Protokół OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).  |
| `state`                 | zalecane | Wartość zawarta w żądaniu, która również zostanie zwrócona w odpowiedzi tokenu. Może to być ciąg dowolnej zawartości, którą chcesz. Losowo generowana unikatowa wartość jest zwykle używana do [zapobiegania atakom fałszerskości żądań między witrynami.](https://tools.ietf.org/html/rfc6749#section-10.12) Wartość może również kodować informacje o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelnienia, takie jak strona lub widok, na których się znajdowały. |
| `prompt`  | optional    | Wskazuje typ interakcji użytkownika, który jest wymagany. Jedynymi prawidłowymi wartościami `login` `none`w `consent`tej chwili są , i .<br/><br/>- `prompt=login`wymusi użytkownikowi wprowadzenie poświadczeń w tym żądaniu, negując jednokrotne zalogowanie.<br/>- `prompt=none`jest odwrotnie - zapewni, że użytkownik nie zostanie przedstawiony z żadnym interaktywnym monitem. Jeśli nie można ukończyć żądania po cichu za pomocą logowania jednokrotnego, punkt końcowy platformy tożsamości firmy Microsoft zwróci błąd. `interaction_required`<br/>- `prompt=consent`wyzwoli okno dialogowe zgody OAuth po zalogowaniu się użytkownika, prosząc użytkownika o przyznanie uprawnień do aplikacji.<br/>- `prompt=select_account`przerywa logowanie jednokrotne, zapewniając doświadczenie wyboru konta, wymieniając wszystkie konta w sesji lub zapamiętanym koncie lub opcję całkowitego użycia innego konta.<br/> |
| `login_hint`  | optional    | Może służyć do wstępnego wypełnienia pola nazwy użytkownika/adresu e-mail strony logowania dla użytkownika, jeśli znasz jego nazwę użytkownika z wyprzedzeniem. Często aplikacje będą używać tego parametru podczas ponownego uwierzytelniania, po wyodrębnieniu nazwy użytkownika z poprzedniego logowania przy użyciu `preferred_username` oświadczenia.   |
| `domain_hint`  | optional    | Może być `consumers` jednym `organizations`z lub .<br/><br/>Jeśli zostanie uwzględniony, pominie proces odnajdywania opartego na wiadomości e-mail, który użytkownik przechodzi na stronie logowania, co prowadzi do nieco bardziej usprawnione środowisko użytkownika. Często aplikacje będą używać tego parametru podczas `tid` ponownego uwierzytelniania, wyodrębniając z poprzedniego logowania. Jeśli `tid` wartością `9188040d-6c67-4c5b-b112-36a304b66dad`oświadczenia jest `domain_hint=consumers`użycie . W przeciwnym `domain_hint=organizations`razie należy użyć pliku .  |
| `code_challenge_method` | optional    | Metoda używana do kodowania `code_verifier` `code_challenge` dla parametru. Może być jedną z następujących wartości:<br/><br/>- `plain` <br/>- `S256`<br/><br/>Jeśli jest `code_challenge` wykluczona, przyjmuje `code_challenge` się, że jest to zwykły tekst, jeśli jest dołączona. Platforma tożsamości firmy `plain` `S256`Microsoft obsługuje zarówno i . Aby uzyskać więcej informacji, zobacz [PKCE RFC](https://tools.ietf.org/html/rfc7636). |
| `code_challenge`  | optional | Służy do zabezpieczania dotacji kodu autoryzacji za pośrednictwem klucza dowodu wymiany kodu (PKCE) od klienta macierzystego. Wymagane, `code_challenge_method` jeśli jest wliczone w cenę. Aby uzyskać więcej informacji, zobacz [PKCE RFC](https://tools.ietf.org/html/rfc7636). |

W tym momencie użytkownik zostanie poproszony o wprowadzenie swoich poświadczeń i zakończenie uwierzytelniania. Punkt końcowy platformy tożsamości firmy Microsoft zapewni również, że użytkownik wyraził `scope` zgodę na uprawnienia wskazane w parametrze zapytania. Jeśli użytkownik nie wyraził zgody na żadne z tych uprawnień, poprosi użytkownika o zgodę na wymagane uprawnienia. Szczegółowe informacje o [uprawnieniach, zgodach i aplikacjach z wieloma dzierżawcami znajdują się tutaj](v2-permissions-and-consent.md).

Gdy użytkownik uwierzytelnia się i udzieli zgody, punkt końcowy platformy tożsamości firmy `redirect_uri`Microsoft zwróci odpowiedź do `response_mode` aplikacji we wskazanym , przy użyciu metody określonej w parametrze.

#### <a name="successful-response"></a>Skuteczna reakcja

Pomyślna odpowiedź `response_mode=query` za pomocą wygląda następująco:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| Parametr | Opis  |
|-----------|--------------|
| `code` | Authorization_code, o które prosiła aplikacja. Aplikacja może użyć kodu autoryzacji, aby zażądać tokenu dostępu dla zasobu docelowego. Authorization_codes są krótkotrwałe, zazwyczaj wygasają po około 10 minutach. |
| `state` | Jeśli parametr stanu znajduje się w żądaniu, ta sama wartość powinna pojawić się w odpowiedzi. Aplikacja powinna sprawdzić, czy wartości stanu w żądaniu i odpowiedzi są identyczne. |

#### <a name="error-response"></a>Odpowiedź na błąd

Odpowiedzi na błędy mogą być `redirect_uri` również wysyłane do aplikacji, dzięki czemu aplikacja może obsługiwać je odpowiednio:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parametr | Opis  |
|----------|------------------|
| `error`  | Ciąg kodu błędu, który może służyć do klasyfikowania typów błędów, które występują i może służyć do reagowania na błędy. |
| `error_description` | Określony komunikat o błędzie, który może pomóc deweloperowi zidentyfikować główną przyczynę błędu uwierzytelniania. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Kody błędów dla błędów punktu końcowego autoryzacji

W poniższej tabeli opisano różne kody `error` błędów, które mogą być zwracane w parametrze odpowiedzi na błąd.

| Kod błędu  | Opis    | Akcja klienta   |
|-------------|----------------|-----------------|
| `invalid_request` | Błąd protokołu, taki jak brak wymaganego parametru. | Napraw i ponownie prześlij żądanie. Jest to błąd rozwoju zazwyczaj złowionych podczas początkowego testowania. |
| `unauthorized_client` | Aplikacja kliencka nie może żądać kodu autoryzacji. | Ten błąd zwykle występuje, gdy aplikacja kliencka nie jest zarejestrowana w usłudze Azure AD lub nie jest dodawany do dzierżawy usługi Azure AD użytkownika. Aplikacja może monitować użytkownika o instrukcje dotyczące instalowania aplikacji i dodawania jej do usługi Azure AD. |
| `access_denied`  | Właściciel zasobu, który odmówił zgody  | Aplikacja kliencka może powiadomić użytkownika, że nie może kontynuować, chyba że użytkownik wyrazi na to zgodę. |
| `unsupported_response_type` | Serwer autoryzacji nie obsługuje typu odpowiedzi w żądaniu. | Napraw i ponownie prześlij żądanie. Jest to błąd rozwoju zazwyczaj złowionych podczas początkowego testowania.  |
| `server_error`  | Serwer napotkał nieoczekiwany błąd.| Ponów próbę żądania. Błędy te mogą wynikać z warunków tymczasowych. Aplikacja kliencka może wyjaśnić użytkownikowi, że jego odpowiedź jest opóźniona na błąd tymczasowy. |
| `temporarily_unavailable`   | Serwer jest tymczasowo zbyt zajęty, aby obsłużyć żądanie. | Ponów próbę żądania. Aplikacja kliencka może wyjaśnić użytkownikowi, że jego odpowiedź jest opóźniona z powodu stanu tymczasowego. |
| `invalid_resource`  | Zasób docelowy jest nieprawidłowy, ponieważ nie istnieje, usługa Azure AD nie może go znaleźć lub nie jest poprawnie skonfigurowany. | Ten błąd wskazuje, że zasób, jeśli istnieje, nie został skonfigurowany w dzierżawie. Aplikacja może monitować użytkownika o instrukcje dotyczące instalowania aplikacji i dodawania jej do usługi Azure AD. |
| `login_required` | Znaleziono zbyt wielu użytkowników lub nie znaleziono ich | Klient zażądał uwierzytelnienia`prompt=none`dyskretnego ( ), ale nie można odnaleźć ani jednego użytkownika. Może to oznaczać, że w sesji jest aktywnych wielu użytkowników lub nie ma użytkowników. Uwzględnia to wybranej dzierżawy (na przykład, jeśli istnieją dwa konta usługi `consumers` Azure AD aktywne i jedno konto Microsoft i jest wybierany, uwierzytelnianie dyskretne będzie działać). |
| `interaction_required` | Żądanie wymaga interakcji z użytkownikiem. | Wymagany jest dodatkowy krok uwierzytelniania lub zgoda. Ponów próbę `prompt=none`żądania bez pliku . |

## <a name="request-an-access-token"></a>Żądanie tokenu dostępu

Teraz, gdy nabyliśmy authorization_code i zostały przyznane uprawnienia przez użytkownika, można `code` wymienić dla żądanego `access_token` zasobu. Aby to zrobić, `POST` wysyłając `/token` żądanie do punktu końcowego:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps. This secret needs to be URL-Encoded.
```

> [!TIP]
> Spróbuj wykonać to żądanie w Postman! (Nie zapomnij wymienić `code`) [Spróbuj uruchomić to żądanie w postman ![](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

| Parametr  | Wymagane/opcjonalne | Opis     |
|------------|-------------------|----------------|
| `tenant`   | wymagany   | Wartość `{tenant}` w ścieżce żądania może służyć do kontrolowania, kto może zalogować się do aplikacji. Dozwolone wartości to `common` `organizations`, `consumers`, i identyfikatory dzierżawy. Aby uzyskać więcej informacji, zobacz [podstawy protokołu](active-directory-v2-protocols.md#endpoints).  |
| `client_id` | wymagany  | Identyfikator aplikacji (klienta), który [strona azure portal — rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) jest przypisana do aplikacji. |
| `grant_type` | wymagany   | Musi `authorization_code` być dla przepływu kodu autoryzacji.   |
| `scope`      | wymagany   | Oddzielona spacja lista zakresów. Zakresy wymagane w tej części muszą być równoważne lub podzbiór zakresów wymaganych w pierwszym etapie. Wszystkie zakresy muszą pochodzić z jednego zasobu, wraz`profile` `openid`z `email`zakresami OIDC ( , , ). Aby uzyskać bardziej szczegółowe wyjaśnienie zakresów, zapoznaj się z [uprawnieniami, zgodą i zakresami](v2-permissions-and-consent.md). |
| `code`          | wymagany  | Authorization_code, które nabyłeś w pierwszym etapie przepływu. |
| `redirect_uri`  | wymagany  | Ta sama wartość redirect_uri, która została wykorzystana do nabycia authorization_code. |
| `client_secret` | wymagane dla aplikacji internetowych | Klucz tajny aplikacji utworzony w portalu rejestracji aplikacji dla aplikacji. Nie należy używać klucza tajnego aplikacji w aplikacji macierzystej, ponieważ client_secrets nie mogą być niezawodnie przechowywane na urządzeniach. Jest to wymagane dla aplikacji sieci web i interfejsów API sieci Web, które mają możliwość przechowywania client_secret bezpiecznie po stronie serwera.  Klucz tajny klienta musi być zakodowany w adresie URL przed wysłaniem. Aby uzyskać więcej informacji, kliknij [tutaj](https://tools.ietf.org/html/rfc3986#page-12). |
| `code_verifier` | optional  | Ten sam code_verifier, który został użyty do uzyskania authorization_code. Wymagane, jeśli PKCE był używany w żądaniu udzielenia kodu autoryzacji. Aby uzyskać więcej informacji, zobacz [PKCE RFC](https://tools.ietf.org/html/rfc7636). |

### <a name="successful-response"></a>Skuteczna reakcja

Pomyślna odpowiedź tokenu będzie wyglądać następująco:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```

| Parametr     | Opis   |
|---------------|------------------------------|
| `access_token`  | Żądany token dostępu. Aplikacja może używać tego tokenu do uwierzytelniania do zabezpieczonego zasobu, takiego jak internetowy interfejs API.  |
| `token_type`    | Wskazuje wartość typu tokenu. Jedynym typem, który obsługuje usługę Azure AD, jest obiekt Bearer |
| `expires_in`    | Jak długo token dostępu jest prawidłowy (w sekundach). |
| `scope`         | Zakresy, dla których access_token jest prawidłowa. |
| `refresh_token` | Token odświeżania OAuth 2.0. Aplikacja może użyć tego tokenu uzyskać dodatkowe tokeny dostępu po wygaśnięciu bieżącego tokenu dostępu. Refresh_tokens są długotrwałe i mogą być używane do zachowania dostępu do zasobów przez dłuższy czas. Aby uzyskać więcej informacji na temat odświeżania tokenu dostępu, zapoznaj się z [poniższą sekcją](#refresh-the-access-token). <br> **Uwaga:** Tylko pod `offline_access` warunkiem, że zakres był wymagany. |
| `id_token`      | Token sieci Web JSON (JWT). Aplikacja może dekodować segmenty tego tokenu, aby zażądać informacji o użytkowniku, który się zalogował. Aplikacja może buforować wartości i wyświetlać je, ale nie należy polegać na nich dla żadnych granic autoryzacji lub zabezpieczeń. Aby uzyskać więcej informacji na temat [`id_token reference`](id-tokens.md)id_tokens, zobacz . <br> **Uwaga:** Tylko pod `openid` warunkiem, że zakres był wymagany. |

### <a name="error-response"></a>Odpowiedź na błąd

Odpowiedzi na błędy będą wyglądać następująco:

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parametr         | Opis    |
|-------------------|----------------|
| `error`       | Ciąg kodu błędu, który może służyć do klasyfikowania typów błędów, które występują i może służyć do reagowania na błędy. |
| `error_description` | Określony komunikat o błędzie, który może pomóc deweloperowi zidentyfikować główną przyczynę błędu uwierzytelniania. |
| `error_codes` | Lista kodów błędów specyficznych dla STS, które mogą pomóc w diagnostyce.  |
| `timestamp`   | Czas, w którym wystąpił błąd. |
| `trace_id`    | Unikatowy identyfikator żądania, który może pomóc w diagnostyce. |
| `correlation_id` | Unikatowy identyfikator żądania, który może pomóc w diagnostyce między składnikami. |

### <a name="error-codes-for-token-endpoint-errors"></a>Kody błędów dla błędów tokenu końcowego

| Kod błędu         | Opis        | Akcja klienta    |
|--------------------|--------------------|------------------|
| `invalid_request`  | Błąd protokołu, taki jak brak wymaganego parametru. | Napraw i ponownie prześlij żądanie   |
| `invalid_grant`    | Kod autoryzacyjny lub weryfikator kodu PKCE jest nieprawidłowy lub wygasł. | Spróbuj nowego żądania `/authorize` do punktu końcowego i sprawdź, czy parametr code_verifier był poprawny.  |
| `unauthorized_client` | Uwierzytelniony klient nie jest upoważniony do używania tego typu dotacji autoryzacji. | Zwykle dzieje się tak, gdy aplikacja kliencka nie jest zarejestrowana w usłudze Azure AD lub nie jest dodawana do dzierżawy usługi Azure AD użytkownika. Aplikacja może monitować użytkownika o instrukcje dotyczące instalowania aplikacji i dodawania jej do usługi Azure AD. |
| `invalid_client` | Uwierzytelnianie klienta nie powiodło się.  | Poświadczenia klienta są nieprawidłowe. Aby naprawić, administrator aplikacji aktualizuje poświadczenia.   |
| `unsupported_grant_type` | Serwer autoryzacji nie obsługuje typu udzielania autoryzacji. | Zmień typ dotacji w żądaniu. Ten typ błędu powinien występować tylko podczas opracowywania i być wykryty podczas wstępnego testowania. |
| `invalid_resource` | Zasób docelowy jest nieprawidłowy, ponieważ nie istnieje, usługa Azure AD nie może go znaleźć lub nie jest poprawnie skonfigurowany. | Oznacza to, że zasób, jeśli istnieje, nie został skonfigurowany w dzierżawie. Aplikacja może monitować użytkownika o instrukcje dotyczące instalowania aplikacji i dodawania jej do usługi Azure AD.  |
| `interaction_required` | Żądanie wymaga interakcji z użytkownikiem. Na przykład wymagany jest dodatkowy krok uwierzytelniania. | Ponów próbę żądania za pomocą tego samego zasobu.  |
| `temporarily_unavailable` | Serwer jest tymczasowo zbyt zajęty, aby obsłużyć żądanie. | Ponów próbę żądania. Aplikacja kliencka może wyjaśnić użytkownikowi, że jego odpowiedź jest opóźniona z powodu stanu tymczasowego. |

## <a name="use-the-access-token"></a>Korzystanie z tokenu dostępu

Teraz, po pomyślnym `access_token`nabyciu , można użyć tokenu w żądaniach `Authorization` do interfejsów API sieci web, dołączając go do nagłówka:

> [!TIP]
> Wykonaj to żądanie w postman! (Najpierw `Authorization` zastąp nagłówek) [Spróbuj uruchomić to żądanie w postman ![](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-access-token"></a>Odświeżanie tokenu dostępu

Access_tokens są krótkotrwałe i należy je odświeżyć po ich wygaśnięciu, aby kontynuować dostęp do zasobów. Można to zrobić, przesyłając `POST` kolejne `/token` żądanie do punktu `refresh_token` końcowego, `code`tym razem zapewniając zamiast .  Tokeny odświeżania są prawidłowe dla wszystkich uprawnień, na które klient otrzymał już `scope=mail.read` zgodę — w związku z `scope=api://contoso.com/api/UseResource`tym token odświeżania wystawiony na żądanie może zostać użyty do żądania nowego tokenu dostępu dla .

Tokeny odświeżania nie mają określonych okresów istnienia. Zazwyczaj okresy istnienia tokenów odświeżania są stosunkowo długie. Jednak w niektórych przypadkach tokeny odświeżania wygasają, są odwoływane lub nie mają wystarczających uprawnień do żądanej akcji. Aplikacja musi oczekiwać i obsługiwać [błędy zwracane przez punkt końcowy wystawienia tokenu](#error-codes-for-token-endpoint-errors) poprawnie.

Chociaż tokeny odświeżania nie są odwoływane, gdy są używane do uzyskiwania nowych tokenów dostępu, oczekuje się, aby odrzucić stary token odświeżania. [Specyfikacja OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-6) mówi: "Serwer autoryzacji może wydać nowy token odświeżania, w którym to przypadku klient musi odrzucić stary token odświeżania i zastąpić go nowym tokenem odświeżania. Serwer autoryzacji może odwołać stary token odświeżania po wydaniu nowego tokenu odświeżania do klienta."

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&client_secret=JqQX2PNo9bpM0uEihUPzyrh      // NOTE: Only required for web apps. This secret needs to be URL-Encoded
```

> [!TIP]
> Spróbuj wykonać to żądanie w Postman! (Nie zapomnij wymienić `refresh_token`) [Spróbuj uruchomić to żądanie w postman ![](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)
>

| Parametr     |                | Opis        |
|---------------|----------------|--------------------|
| `tenant`        | wymagany     | Wartość `{tenant}` w ścieżce żądania może służyć do kontrolowania, kto może zalogować się do aplikacji. Dozwolone wartości to `common` `organizations`, `consumers`, i identyfikatory dzierżawy. Aby uzyskać więcej informacji, zobacz [podstawy protokołu](active-directory-v2-protocols.md#endpoints).   |
| `client_id`     | wymagany    | **Identyfikator aplikacji (klienta),** który usługa Azure portal — środowisko [rejestracji aplikacji przypisane](https://go.microsoft.com/fwlink/?linkid=2083908) do aplikacji. |
| `grant_type`    | wymagany    | Musi `refresh_token` być dla tej części przepływu kodu autoryzacji. |
| `scope`         | wymagany    | Oddzielona spacja lista zakresów. Zakresy wymagane w tej części muszą być równoważne lub podzbiór zakresów wymaganych w pierwotnym authorization_code odcinek żądania. Jeśli zakresy określone w tym żądaniu obejmują wiele serwerów zasobów, punkt końcowy platformy tożsamości firmy Microsoft zwróci token dla zasobu określonego w pierwszym zakresie. Aby uzyskać bardziej szczegółowe wyjaśnienie zakresów, zapoznaj się z [uprawnieniami, zgodą i zakresami](v2-permissions-and-consent.md). |
| `refresh_token` | wymagany    | Refresh_token, które nabyłeś w drugiej części przepływu. |
| `client_secret` | wymagane dla aplikacji internetowych | Klucz tajny aplikacji utworzony w portalu rejestracji aplikacji dla aplikacji. Nie należy go używać w aplikacji macierzystej, ponieważ nie można niezawodnie przechowywać client_secrets na urządzeniach. Jest to wymagane dla aplikacji sieci web i interfejsów API sieci Web, które mają możliwość przechowywania client_secret bezpiecznie po stronie serwera. Ten sekret musi być zakodowany w adresie URL, aby uzyskać więcej informacji, kliknij [tutaj](https://tools.ietf.org/html/rfc3986#page-12). |

#### <a name="successful-response"></a>Skuteczna reakcja

Pomyślna odpowiedź tokenu będzie wyglądać następująco:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| Parametr     | Opis         |
|---------------|-------------------------------------------------------------|
| `access_token`  | Żądany token dostępu. Aplikacja może używać tego tokenu do uwierzytelniania do zabezpieczonego zasobu, takiego jak internetowy interfejs API. |
| `token_type`    | Wskazuje wartość typu tokenu. Jedynym typem, który obsługuje usługę Azure AD, jest obiekt Bearer |
| `expires_in`    | Jak długo token dostępu jest prawidłowy (w sekundach).   |
| `scope`         | Zakresy, dla których access_token jest prawidłowa.    |
| `refresh_token` | Nowy token odświeżania OAuth 2.0. Należy zastąpić stary token odświeżania tym nowo nabytym tokenem odświeżania, aby upewnić się, że tokeny odświeżania pozostają ważne tak długo, jak to możliwe. <br> **Uwaga:** Tylko pod `offline_access` warunkiem, że zakres był wymagany.|
| `id_token`      | Niepodpisany token JSON Web Token (JWT). Aplikacja może dekodować segmenty tego tokenu, aby zażądać informacji o użytkowniku, który się zalogował. Aplikacja może buforować wartości i wyświetlać je, ale nie należy polegać na nich dla żadnych granic autoryzacji lub zabezpieczeń. Aby uzyskać więcej informacji na temat [`id_token reference`](id-tokens.md)id_tokens, zobacz . <br> **Uwaga:** Tylko pod `openid` warunkiem, że zakres był wymagany. |

#### <a name="error-response"></a>Odpowiedź na błąd

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parametr         | Opis                                                                                        |
|-------------------|----------------------------------------------------------------------------------------------------|
| `error`           | Ciąg kodu błędu, który może służyć do klasyfikowania typów błędów, które występują i może służyć do reagowania na błędy. |
| `error_description` | Określony komunikat o błędzie, który może pomóc deweloperowi zidentyfikować główną przyczynę błędu uwierzytelniania.           |
| `error_codes` |Lista kodów błędów specyficznych dla STS, które mogą pomóc w diagnostyce. |
| `timestamp` | Czas, w którym wystąpił błąd. |
| `trace_id` | Unikatowy identyfikator żądania, który może pomóc w diagnostyce. |
| `correlation_id` | Unikatowy identyfikator żądania, który może pomóc w diagnostyce między składnikami. |

Aby uzyskać opis kodów błędów i zalecanej akcji klienta, zobacz [Kody błędów dla błędów tokenu końcowego](#error-codes-for-token-endpoint-errors).
