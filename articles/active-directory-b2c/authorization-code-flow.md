---
title: Przepływ kodu autoryzacji — Azure Active Directory B2C | Microsoft Docs
description: Dowiedz się, jak tworzyć aplikacje sieci Web za pomocą protokołu uwierzytelniania Azure AD B2C i OpenID Connect Connect.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 9ae5632f2495ac5916ac8c86666e973c34d1b789
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92215233"
---
# <a name="oauth-20-authorization-code-flow-in-azure-active-directory-b2c"></a>Przepływ kodu autoryzacji OAuth 2,0 w Azure Active Directory B2C

Aby uzyskać dostęp do chronionych zasobów, takich jak interfejsy API sieci Web, można użyć przyznawania kodu autoryzacji OAuth 2,0 w aplikacjach zainstalowanych na urządzeniu. Korzystając z implementacji Azure Active Directory B2C (Azure AD B2C) protokołu OAuth 2,0, można dodawać zadania rejestracji, logowania i innych zadań związanych z zarządzaniem tożsamościami do aplikacji na jednej stronie, na urządzeniach przenośnych i klasycznych. Ten artykuł jest niezależny od języka. W tym artykule opisano sposób wysyłania i odbierania komunikatów HTTP bez używania bibliotek Open Source. Jeśli to możliwe, zalecamy korzystanie z obsługiwanych bibliotek uwierzytelniania firmy Microsoft (MSAL). Zapoznaj się z [przykładowymi aplikacjami korzystającymi z MSAL](code-samples.md).

Przepływ kodu autoryzacji OAuth 2,0 został opisany w [sekcji 4,1 specyfikacji oauth 2,0](https://tools.ietf.org/html/rfc6749). Można jej używać do uwierzytelniania i autoryzacji w przypadku większości [typów aplikacji](application-types.md), w tym aplikacji sieci Web, aplikacji jednostronicowych i natywnie zainstalowanych aplikacji. Przepływ kodu autoryzacji OAuth 2,0 umożliwia bezpieczne pozyskiwanie tokenów dostępu i odświeżanie tokenów aplikacji, które mogą być używane w celu uzyskania dostępu do zasobów zabezpieczonych przez [serwer autoryzacji](protocols-overview.md).  Token odświeżania pozwala klientowi uzyskać nowe tokeny dostępu (i odświeżania) po wygaśnięciu tokenu dostępu, zwykle po godzinie.

<!-- This article focuses on the **public clients** OAuth 2.0 authorization code flow. A public client is any client application that cannot be trusted to securely maintain the integrity of a secret password. This includes single-page applications, mobile apps, desktop applications, and essentially any application that runs on a device and needs to get access tokens. -->

> [!NOTE]
> Aby dodać Zarządzanie tożsamościami do aplikacji sieci Web przy użyciu Azure AD B2C, użyj funkcji [OpenID Connect Connect](openid-connect.md) zamiast uwierzytelniania OAuth 2,0.

Azure AD B2C rozszerza standardowe przepływy protokołu OAuth 2,0, aby wykonywać więcej niż proste uwierzytelnianie i autoryzację. Wprowadza [przepływ użytkownika](user-flow-overview.md). Za pomocą przepływów użytkowników można używać protokołu OAuth 2,0 do dodawania środowisk użytkownika do aplikacji, takich jak rejestrowanie, logowanie i Zarządzanie profilem. Dostawcy tożsamości, którzy korzystają z protokołu OAuth 2,0, obejmują [Amazon](identity-provider-amazon.md), [Azure Active Directory](identity-provider-azure-ad-single-tenant.md), [Facebook](identity-provider-facebook.md), [GitHub](identity-provider-github.md), [Google](identity-provider-google.md)i [LinkedIn](identity-provider-linkedin.md).

Aby wypróbować żądania HTTP w tym artykule:

1. Zamień `{tenant}` na nazwę dzierżawy Azure AD B2C.
1. Zamień na `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` Identyfikator aplikacji aplikacji, która została wcześniej zarejestrowana w dzierżawie Azure AD B2C.
1. Zamień na `{policy}` nazwę zasad utworzonych w dzierżawie, na przykład `b2c_1_sign_in` .

## <a name="redirect-uri-setup-required-for-single-page-apps"></a>Wymagana jest konfiguracja identyfikatora URI przekierowania dla aplikacji jednostronicowych

Przepływ kodu autoryzacji dla aplikacji jednostronicowych wymaga dodatkowej konfiguracji.  Postępuj zgodnie z instrukcjami dotyczącymi [tworzenia aplikacji jednostronicowych](tutorial-register-spa.md) , aby poprawnie oznaczyć identyfikator URI przekierowania jako włączony dla mechanizmu CORS. Aby zaktualizować istniejący identyfikator URI przekierowania w celu włączenia mechanizmu CORS, Otwórz Edytor manifestu i ustaw `type` pole dla identyfikatora URI przekierowania na `spa` w `replyUrlsWithType` sekcji. Możesz również kliknąć identyfikator URI przekierowania w sekcji "Web" na karcie uwierzytelnianie i wybrać identyfikatory URI, które mają zostać zmigrowane, aby użyć przepływu kodu autoryzacji.

`spa`Typ przekierowania jest wstecznie zgodny z niejawnym przepływem. Aplikacje używające obecnie niejawnego przepływu do uzyskiwania tokenów mogą zostać przeniesione do `spa` typu identyfikatora URI przekierowania bez problemów i nadal przy użyciu niejawnego przepływu.

W przypadku próby użycia przepływu kodu autoryzacji i wyświetlenia tego błędu:

`access to XMLHttpRequest at 'https://login.microsoftonline.com/common/v2.0/oauth2/token' from origin 'yourApp.com' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.`

Następnie należy odwiedzić rejestrację aplikacji i zaktualizować identyfikator URI przekierowania dla aplikacji do typu `spa` .

## <a name="1-get-an-authorization-code"></a>1. Pobierz kod autoryzacji
Przepływ kodu autoryzacji zaczyna się od klienta kierującego użytkownika do `/authorize` punktu końcowego. Jest to interaktywna część przepływu, w której użytkownik wykonuje akcję. W tym żądaniu klient wskazuje w `scope` parametrze uprawnienia wymagane do uzyskania od użytkownika. Poniższe trzy przykłady (z podziałami wierszy na potrzeby czytelności) używają innego przepływu użytkownika.


```http
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&code_challenge=YTFjNjI1OWYzMzA3MTI4ZDY2Njg5M2RkNmVjNDE5YmEyZGRhOGYyM2IzNjdmZWFhMTQ1ODg3NDcxY2Nl
&code_challenge_method=S256
```

| Parametr | Wymagane? | Opis |
| --- | --- | --- |
|dzierżaw| Wymagane | Nazwa dzierżawy Azure AD B2C|
| zasad | Wymagane | Przepływ użytkownika do uruchomienia. Określ nazwę przepływu użytkownika utworzonego w dzierżawie Azure AD B2C. Na przykład: `b2c_1_sign_in` , `b2c_1_sign_up` , lub `b2c_1_edit_profile` . |
| client_id |Wymagane |Identyfikator aplikacji przypisany do aplikacji w [Azure Portal](https://portal.azure.com). |
| response_type |Wymagane |Typ odpowiedzi, który musi obejmować `code` przepływ kodu autoryzacji. |
| redirect_uri |Wymagane |Identyfikator URI przekierowania aplikacji, w którym odpowiedzi uwierzytelniania są wysyłane i odbierane przez aplikację. Musi dokładnie pasować do jednego z identyfikatorów URI przekierowania zarejestrowanych w portalu, z tą różnicą, że musi być zakodowany w adresie URL. |
| scope |Wymagane |Rozdzielana spacjami lista zakresów. Wartość jednego zakresu wskazuje na Azure Active Directory (Azure AD) wymagane uprawnienia. Użycie identyfikatora klienta jako zakresu wskazuje, że aplikacja wymaga tokenu dostępu, który może być używany w odniesieniu do własnej usługi lub internetowego interfejsu API, reprezentowanego przez ten sam identyfikator klienta.  `offline_access`Zakres wskazuje, że aplikacja wymaga tokenu odświeżania na potrzeby długotrwałego dostępu do zasobów. Można również użyć zakresu, `openid` Aby zażądać tokenu identyfikatora z Azure AD B2C. |
| response_mode |Zalecane |Metoda używana do wysyłania podanego kodu autoryzacji z powrotem do aplikacji. Może to być `query` , `form_post` , lub `fragment` . |
| stan |Zalecane |Wartość zawarta w żądaniu, która może być ciągiem dowolnej zawartości, która ma być używana. Zwykle jest używana losowo wygenerowana unikatowa wartość, aby zapobiec atakom na fałszowanie żądań między lokacjami. Ten stan jest również używany do kodowania informacji o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelnienia. Na przykład strona, na której znajduje się użytkownik, lub przepływ użytkownika, który był wykonywany. |
| pytać |Optional |Typ interakcji z użytkownikiem, który jest wymagany. Obecnie jedyną prawidłową wartością jest `login` , która wymusza, aby użytkownik wprowadził swoje poświadczenia dla tego żądania. Logowanie jednokrotne nie zacznie obowiązywać. |
| code_challenge  | zalecane/wymagane | Używane do zabezpieczania kodu autoryzacji za pośrednictwem klucza testowego dla wymiany kodu (PKCE). Wymagane, jeśli `code_challenge_method` jest dołączony. Aby uzyskać więcej informacji, zobacz [dokument RFC PKCE](https://tools.ietf.org/html/rfc7636). Jest to teraz zalecane dla wszystkich typów aplikacji — natywnych aplikacji, aplikacji jednostronicowych i klientów poufnych, takich jak aplikacje sieci Web. | 
| `code_challenge_method` | zalecane/wymagane | Metoda używana do kodowania `code_verifier` `code_challenge` parametru. *Powinno* to być `S256` , ale Specyfikacja umożliwia korzystanie z programu, `plain` Jeśli z jakiegoś powodu klient nie może obsłużyć SHA256. <br/><br/>Jeśli jest wykluczony, przyjmuje się, że jest to `code_challenge` zwykły tekst, jeśli `code_challenge` jest uwzględniony. Platforma tożsamości firmy Microsoft obsługuje programy `plain` i `S256` . Aby uzyskać więcej informacji, zobacz [dokument RFC PKCE](https://tools.ietf.org/html/rfc7636). Jest to wymagane w przypadku [aplikacji jednostronicowych przy użyciu przepływu kodu autoryzacji](tutorial-register-spa.md).|

W tym momencie użytkownik jest proszony o ukończenie przepływu pracy przepływu użytkownika. Może to oznaczać, że użytkownik wprowadza nazwę użytkownika i hasło, logując się przy użyciu tożsamości społecznościowej, rejestruje się w katalogu lub dowolną inną liczbę kroków. Akcje użytkownika zależą od sposobu definiowania przepływu użytkownika.

Po zakończeniu przepływu użytkownika usługa Azure AD zwróci odpowiedź do aplikacji na wartość użytą przez użytkownika `redirect_uri` . Używa metody określonej w `response_mode` parametrze. Odpowiedź jest dokładnie taka sama dla każdego scenariusza akcji użytkownika, niezależnie od przepływu użytkownika, który został wykonany.

Pomyślna odpowiedź, która używa `response_mode=query` tego wyglądu, wygląda następująco:

```http
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| Parametr | Opis |
| --- | --- |
| kod |Kod autoryzacji żądany przez aplikację. Aplikacja może użyć kodu autoryzacji do żądania tokenu dostępu dla zasobu docelowego. Kody autoryzacji są bardzo krótkie. Zazwyczaj wygasają po około 10 minutach. |
| stan |Zapoznaj się z pełnym opisem w tabeli w poprzedniej sekcji. Jeśli `state` parametr zostanie uwzględniony w żądaniu, ta sama wartość powinna pojawić się w odpowiedzi. Aplikacja powinna sprawdzić, czy `state` wartości w żądaniu i odpowiedzi są identyczne. |

Odpowiedzi na błędy można także wysyłać do identyfikatora URI przekierowania, aby aplikacja mogła je odpowiednio obsłużyć:

```http
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametr | Opis |
| --- | --- |
| błąd |Ciąg kodu błędu, którego można użyć do klasyfikowania typów błędów, które wystąpiły. Można również użyć ciągu do reagowania na błędy. |
| error_description |Konkretny komunikat o błędzie, który może pomóc w zidentyfikowaniu głównej przyczyny błędu uwierzytelniania. |
| stan |Zobacz pełny opis w powyższej tabeli. Jeśli `state` parametr zostanie uwzględniony w żądaniu, ta sama wartość powinna pojawić się w odpowiedzi. Aplikacja powinna sprawdzić, czy `state` wartości w żądaniu i odpowiedzi są identyczne. |

## <a name="2-get-an-access-token"></a>2. Uzyskiwanie tokenu dostępu
Po uzyskaniu kodu autoryzacji można zrealizować `code` token dla danego zasobu, wysyłając żądanie post do `/token` punktu końcowego. W Azure AD B2C można [zażądać tokenów dostępu dla innych interfejsów API](access-tokens.md#request-a-token) , jak zwykle, określając ich zakresy w żądaniu.

Możesz również zażądać tokenu dostępu dla własnego internetowego interfejsu API zaplecza w ramach Konwencji przy użyciu identyfikatora klienta aplikacji jako żądanego zakresu (co spowoduje użycie tokenu dostępu z IDENTYFIKATORem klienta jako "odbiorcy"):

```http
POST https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1

Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&code_verifier=ThisIsntRandomButItNeedsToBe43CharactersLong 
```

| Parametr | Wymagane? | Opis |
| --- | --- | --- |
|dzierżaw| Wymagane | Nazwa dzierżawy Azure AD B2C|
|zasad| Wymagane| Przepływ użytkownika, który został użyty do uzyskania kodu autoryzacji. W tym żądaniu nie można użyć innego przepływu użytkownika. |
| client_id |Wymagane |Identyfikator aplikacji przypisany do aplikacji w [Azure Portal](https://portal.azure.com).|
| client_secret | Tak, w Web Apps | Wpis tajny aplikacji, który został wygenerowany w [Azure Portal](https://portal.azure.com/). Wpisy tajne klienta są używane w tym przepływie dla scenariuszy aplikacji sieci Web, w których klient może bezpiecznie przechowywać klucz tajny klienta. W przypadku scenariuszy aplikacji natywnych (klienta publicznego) klucze tajne klienta nie mogą być bezpiecznie przechowywane i w związku z tym nie są używane w tym wywołaniu. Jeśli używasz klucza tajnego klienta, zmień go okresowo. |
| grant_type |Wymagane |Typ dotacji. W przypadku przepływu kodu autoryzacji typem dotacji musi być `authorization_code` . |
| scope |Zalecane |Rozdzielana spacjami lista zakresów. Pojedyncza wartość zakresu wskazuje na usługę Azure AD oba wymagane uprawnienia. Użycie identyfikatora klienta jako zakresu wskazuje, że aplikacja wymaga tokenu dostępu, który może być używany w odniesieniu do własnej usługi lub internetowego interfejsu API, reprezentowanego przez ten sam identyfikator klienta.  `offline_access`Zakres wskazuje, że aplikacja wymaga tokenu odświeżania na potrzeby długotrwałego dostępu do zasobów.  Można również użyć zakresu, `openid` Aby zażądać tokenu identyfikatora z Azure AD B2C. |
| kod |Wymagane |Kod autoryzacji uzyskany w pierwszym etapie przepływu. |
| redirect_uri |Wymagane |Identyfikator URI przekierowania aplikacji, w której został otrzymany kod autoryzacji. |
| code_verifier | zalecane | Ten sam code_verifier, który został użyty w celu uzyskania authorization_code. Wymagane, jeśli w żądaniu udzielenia uprawnienia do kodu autoryzacji użyto PKCE. Aby uzyskać więcej informacji, zobacz [dokument RFC PKCE](https://tools.ietf.org/html/rfc7636). |

Pomyślna odpowiedź dotycząca tokenu wygląda następująco:

```json
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Parametr | Opis |
| --- | --- |
| not_before |Godzina, o której token jest uznawany za ważny, w czasie epoki. |
| token_type |Wartość typu tokenu. Jedynym typem obsługiwanym przez usługę Azure AD jest znak. |
| access_token |Zażądano podpisanego tokenu sieci Web JSON (JWT). |
| scope |Zakresy, dla których token jest ważny. Można również użyć zakresów do buforowania tokenów do późniejszego użycia. |
| expires_in |Czas ważności tokenu (w sekundach). |
| refresh_token |Token odświeżania OAuth 2,0. Aplikacja może użyć tego tokenu, aby uzyskać dodatkowe tokeny po wygaśnięciu bieżącego tokenu. Tokeny odświeżania są długotrwałe. Można ich użyć, aby zachować dostęp do zasobów przez dłuższy czas. Aby uzyskać więcej informacji, zobacz [Informacje o tokenach Azure AD B2C](tokens-overview.md). |

Odpowiedzi na błędy wyglądają następująco:

```json
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametr | Opis |
| --- | --- |
| błąd |Ciąg kodu błędu, którego można użyć do klasyfikowania typów błędów, które wystąpiły. Można również użyć ciągu do reagowania na błędy. |
| error_description |Konkretny komunikat o błędzie, który może pomóc w zidentyfikowaniu głównej przyczyny błędu uwierzytelniania. |

## <a name="3-use-the-token"></a>3. Użyj tokenu
Po pomyślnym pobraniu tokenu dostępu można użyć tokenu w żądaniach do interfejsów API sieci Web zaplecza, dołączając je do `Authorization` nagłówka:

```http
GET /tasks
Host: mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4. Odśwież token
Tokeny dostępu i tokeny identyfikatorów są krótkotrwałe. Po ich wygaśnięciu należy odświeżyć, aby nadal uzyskiwać dostęp do zasobów. W tym celu Prześlij kolejne żądanie POST do `/token` punktu końcowego. Tym razem Podaj `refresh_token` zamiast `code` :

```http
POST https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1

Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parametr | Wymagane? | Opis |
| --- | --- | --- |
|dzierżaw| Wymagane | Nazwa dzierżawy Azure AD B2C|
|zasad |Wymagane |Przepływ użytkownika, który został użyty do uzyskania oryginalnego tokenu odświeżania. W tym żądaniu nie można użyć innego przepływu użytkownika. |
| client_id |Wymagane |Identyfikator aplikacji przypisany do aplikacji w [Azure Portal](https://portal.azure.com). |
| client_secret | Tak, w Web Apps | Wpis tajny aplikacji, który został wygenerowany w [Azure Portal](https://portal.azure.com/). Wpisy tajne klienta są używane w tym przepływie dla scenariuszy aplikacji sieci Web, w których klient może bezpiecznie przechowywać klucz tajny klienta. W przypadku scenariuszy aplikacji natywnych (klienta publicznego) klucze tajne klienta nie mogą być bezpiecznie przechowywane i w związku z tym nie są używane w tym wywołaniu. Jeśli używasz klucza tajnego klienta, zmień go okresowo. |
| grant_type |Wymagane |Typ dotacji. Dla tego odcinka przepływu kodu autoryzacji musi być typem Grant `refresh_token` . |
| scope |Zalecane |Rozdzielana spacjami lista zakresów. Pojedyncza wartość zakresu wskazuje na usługę Azure AD oba wymagane uprawnienia. Użycie identyfikatora klienta jako zakresu wskazuje, że aplikacja wymaga tokenu dostępu, który może być używany w odniesieniu do własnej usługi lub internetowego interfejsu API, reprezentowanego przez ten sam identyfikator klienta.  `offline_access`Zakres wskazuje, że aplikacja będzie potrzebować tokenu odświeżania na potrzeby długotrwałego dostępu do zasobów.  Można również użyć zakresu, `openid` Aby zażądać tokenu identyfikatora z Azure AD B2C. |
| redirect_uri |Optional |Identyfikator URI przekierowania aplikacji, w której został otrzymany kod autoryzacji. |
| refresh_token |Wymagane |Oryginalny token odświeżania uzyskany w drugim etapie przepływu. |

Pomyślna odpowiedź dotycząca tokenu wygląda następująco:

```json
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Parametr | Opis |
| --- | --- |
| not_before |Godzina, o której token jest uznawany za ważny, w czasie epoki. |
| token_type |Wartość typu tokenu. Jedynym typem obsługiwanym przez usługę Azure AD jest znak. |
| access_token |Zażądano podpisanego tokenu JWT. |
| scope |Zakresy, dla których token jest ważny. Można również użyć zakresów do buforowania tokenów do późniejszego użycia. |
| expires_in |Czas ważności tokenu (w sekundach). |
| refresh_token |Token odświeżania OAuth 2,0. Aplikacja może użyć tego tokenu, aby uzyskać dodatkowe tokeny po wygaśnięciu bieżącego tokenu. Tokeny odświeżania są długotrwałe i mogą być używane do przechowywania zasobów przez dłuższy czas. Aby uzyskać więcej informacji, zobacz [Informacje o tokenach Azure AD B2C](tokens-overview.md). |

Odpowiedzi na błędy wyglądają następująco:

```json
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametr | Opis |
| --- | --- |
| błąd |Ciąg kodu błędu, którego można użyć do klasyfikowania typów błędów, które występują. Można również użyć ciągu do reagowania na błędy. |
| error_description |Konkretny komunikat o błędzie, który może pomóc w zidentyfikowaniu głównej przyczyny błędu uwierzytelniania. |

## <a name="use-your-own-azure-ad-b2c-directory"></a>Korzystanie z własnego katalogu Azure AD B2C
Aby wypróbować te żądania samodzielnie, wykonaj następujące czynności. Zastąp przykładowe wartości używane w tym artykule własnymi wartościami.

1. [Utwórz katalog Azure AD B2C](tutorial-create-tenant.md). Użyj nazwy katalogu w żądaniach.
2. [Utwórz aplikację](tutorial-register-applications.md) w celu uzyskania identyfikatora aplikacji i identyfikatora URI przekierowania. Dołącz klienta natywnego do aplikacji.
3. [Tworzenie przepływów użytkowników](user-flow-overview.md) w celu uzyskania nazw przepływów użytkownika.
