---
title: Platforma tożsamości firmy Microsoft i przepływ OAuth2.0 On-Behalf-Of | Azure
titleSuffix: Microsoft identity platform
description: W tym artykule opisano sposób używania komunikatów HTTP do implementowania uwierzytelniania typu usługa-usługa przy użyciu przepływu OAuth 2.0 On-Behalf-Of.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/7/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 1c8ea1580047910cb2d6634aad885d61e99113f3
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529977"
---
# <a name="microsoft-identity-platform-and-oauth-20-on-behalf-of-flow"></a>Platforma tożsamości firmy Microsoft i przepływ OAuth 2.0 On-Behalf-Of


Przepływ OAuth 2.0 On-Behalf-Of (OBO) obsługuje przypadek użycia, w którym aplikacja wywołuje interfejs API usługi/sieci Web, który z kolei musi wywołać inny interfejs API usługi/sieci Web. Chodzi o to, aby propagować delegowaną tożsamość użytkownika i uprawnienia za pośrednictwem łańcucha żądań. Aby usługa warstwy środkowej umożliwiała uwierzytelnianie żądań do usługi podrzędnej, musi zabezpieczyć token dostępu z platformy tożsamości firmy Microsoft w imieniu użytkownika.

W tym artykule opisano, jak programować bezpośrednio przy pomocy protokołu w aplikacji.  Jeśli to możliwe, zalecamy użycie obsługiwanych bibliotek uwierzytelniania firmy Microsoft (MSAL) w celu uzyskania tokenów i wywołania [zabezpieczonych internetowych interfejsów API.](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)  Przyjrzyj się również przykładowym [aplikacjom, które korzystają z pakietu MSAL.](sample-v2-code.md)


Od maja 2018 r. niektórych pochodnych przepływów niejawnych nie można używać `id_token` dla przepływu OBO. Aplikacje jednostronicowe (SPA) powinny przekazywać **token** dostępu do poufnego klienta warstwy środkowej, aby zamiast tego wykonywać przepływy OBO. Aby uzyskać więcej informacji o klientach, którzy mogą wykonywać wywołania OBO, zobacz [ograniczenia](#client-limitations).

## <a name="protocol-diagram"></a>Diagram protokołu

Załóżmy, że użytkownik został uwierzytelniony w aplikacji przy użyciu przepływu przyznawania kodu autoryzacji [OAuth 2.0](v2-oauth2-auth-code-flow.md) lub innego przepływu logowania. W tym momencie aplikacja ma token dostępu dla interfejsu *API A* (token A) z oświadczeniami użytkownika i zgodą na dostęp do internetowego interfejsu API warstwy środkowej (API A). Teraz interfejs API A musi wykonać uwierzytelnione żądanie do podrzędnego internetowego interfejsu API (API B).

Poniższe kroki stanowią przepływ OBO i są objaśnione za pomocą poniższego diagramu.

![Pokazuje przepływ OAuth 2.0 On-Behalf-Of](./media/v2-oauth2-on-behalf-of-flow/protocols-oauth-on-behalf-of-flow.png)

1. Aplikacja kliency wysyła żądanie do interfejsu API A za pomocą tokenu A (z `aud` oświadczeniem interfejsu API A).
1. Interfejs API A uwierzytelnia się w punkcie końcowym wystawiania tokenu platformy tożsamości firmy Microsoft i żąda tokenu w celu uzyskania dostępu do interfejsu API B.
1. Punkt końcowy wystawiania tokenu platformy tożsamości firmy Microsoft weryfikuje poświadczenia interfejsu API A wraz z tokenem A i wydaje token dostępu dla interfejsu API B (token B) do interfejsu API A.
1. Token B jest ustawiany przez interfejs API A w nagłówku autoryzacji żądania do interfejsu API B.
1. Dane z zabezpieczonego zasobu są zwracane przez interfejs API B do interfejsu API A, a następnie do klienta.

W tym scenariuszu usługa warstwy środkowej nie ma interakcji z użytkownikiem w celu uzyskania zgody użytkownika na dostęp do podrzędnego interfejsu API. W związku z tym opcja udzielenia dostępu do podrzędnego interfejsu API jest przedstawiana z góry w ramach kroku zgody podczas uwierzytelniania. Aby dowiedzieć się, jak skonfigurować tę usługę dla aplikacji, zobacz Uzyskiwanie zgody [dla aplikacji warstwy środkowej](#gaining-consent-for-the-middle-tier-application).

## <a name="middle-tier-access-token-request"></a>Żądanie tokenu dostępu warstwy środkowej

Aby zażądać tokenu dostępu, należy wysłać żądanie HTTP POST do punktu końcowego tokenu platformy tożsamości firmy Microsoft specyficznego dla dzierżawy z następującymi parametrami.

```
https://login.microsoftonline.com/<tenant>/oauth2/v2.0/token
```

Istnieją dwa przypadki w zależności od tego, czy aplikacja kliencyjna ma być zabezpieczona za pomocą wspólnego tajnego lub certyfikatu.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Pierwszy przypadek: żądanie tokenu dostępu z udostępnionym kluczem tajnym

W przypadku korzystania ze współużytkowania tajnego żądanie tokenu dostępu między usługami zawiera następujące parametry:

| Parametr | Typ | Opis |
| --- | --- | --- |
| `grant_type` | Wymagane | Typ żądania tokenu. W przypadku żądania używającego JWT wartość musi być wartością `urn:ietf:params:oauth:grant-type:jwt-bearer` . |
| `client_id` | Wymagane | Identyfikator aplikacji (klienta), który [Azure Portal — Rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) przypisano do aplikacji. |
| `client_secret` | Wymagane | Klucz tajny klienta wygenerowany dla aplikacji na stronie Azure Portal — Rejestracje aplikacji aplikacji. |
| `assertion` | Wymagane | Token dostępu, który został wysłany do interfejsu API warstwy środkowej.  Ten token musi zawierać oświadczenie odbiorców () aplikacji, które żąda tego `aud` OBO (aplikacja oznaczona przez `client-id` pole ). Aplikacje nie mogą zrealizować tokenu dla innej aplikacji (np. jeśli klient wysyła do interfejsu API token przeznaczony dla programu MS Graph, interfejs API nie może zrealizować go przy użyciu funkcji OBO.  Zamiast tego należy odrzucić token).  |
| `scope` | Wymagane | Rozdzielana spacjami lista zakresów dla żądania tokenu. Aby uzyskać więcej informacji, zobacz [zakresy](v2-permissions-and-consent.md). |
| `requested_token_use` | Wymagane | Określa sposób przetwarzania żądania. W przepływie OBO wartość musi być ustawiona na `on_behalf_of` . |

#### <a name="example"></a>Przykład

Poniższy wpis HTTP POST żąda tokenu dostępu i odświeża token `user.read` z zakresem dla internetowego https://graph.microsoft.com interfejsu API.

```HTTP
//line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com/<tenant>
Content-Type: application/x-www-form-urlencoded

grant_type=urn:ietf:params:oauth:grant-type:jwt-bearer
&client_id=2846f71b-a7a4-4987-bab3-760035b2f389
&client_secret=BYyVnAt56JpLwUcyo47XODd
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiIyO{a lot of characters here}
&scope=https://graph.microsoft.com/user.read+offline_access
&requested_token_use=on_behalf_of
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Drugi przypadek: żądanie tokenu dostępu z certyfikatem

Żądanie tokenu dostępu typu usługa-usługa z certyfikatem zawiera następujące parametry:

| Parametr | Typ | Opis |
| --- | --- | --- |
| `grant_type` | Wymagane | Typ żądania tokenu. W przypadku żądania używającego JWT wartość musi być wartością `urn:ietf:params:oauth:grant-type:jwt-bearer` . |
| `client_id` | Wymagane |  Identyfikator aplikacji (klienta), który [Azure Portal — Rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) przypisano do aplikacji. |
| `client_assertion_type` | Wymagane | Musi to być wartość `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` . |
| `client_assertion` | Wymagane | Potwierdzenie (token internetowy JSON), które należy utworzyć i podpisać przy użyciu certyfikatu zarejestrowanego jako poświadczenia dla aplikacji. Aby dowiedzieć się, jak zarejestrować certyfikat i format potwierdzenia, zobacz [poświadczenia certyfikatu](active-directory-certificate-credentials.md). |
| `assertion` | Wymagane |  Token dostępu, który został wysłany do interfejsu API warstwy środkowej.  Ten token musi zawierać oświadczenie odbiorców () aplikacji, które żąda tego `aud` OBO (aplikacja oznaczona przez `client-id` pole ). Aplikacje nie mogą zrealizować tokenu dla innej aplikacji (np. jeśli klient wysyła do interfejsu API token przeznaczony dla programu MS Graph, interfejs API nie może zrealizować go przy użyciu funkcji OBO.  Zamiast tego należy odrzucić token).  |
| `requested_token_use` | Wymagane | Określa sposób przetwarzania żądania. W przepływie OBO wartość musi być ustawiona na `on_behalf_of` . |
| `scope` | Wymagane | Rozdzielana spacjami lista zakresów dla żądania tokenu. Aby uzyskać więcej informacji, zobacz [zakresy](v2-permissions-and-consent.md).|

Zwróć uwagę, że parametry są prawie takie same jak w przypadku żądania przez wspólny klucz tajny, z tą różnicą, że parametr jest zastępowany przez dwa `client_secret` parametry: a `client_assertion_type` i `client_assertion` .

#### <a name="example"></a>Przykład

Poniższy wpis HTTP żąda tokenu dostępu z `user.read` zakresem dla internetowego https://graph.microsoft.com interfejsu API z certyfikatem.

```HTTP
// line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com/<tenant>
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiO{a lot of characters here}
&requested_token_use=on_behalf_of
&scope=https://graph.microsoft.com/user.read+offline_access
```

## <a name="middle-tier-access-token-response"></a>Odpowiedź tokenu dostępu warstwy środkowej

Odpowiedzią na powodzenie jest odpowiedź JSON OAuth 2.0 z następującymi parametrami.

| Parametr | Opis |
| --- | --- |
| `token_type` | Wskazuje wartość typu tokenu. Jedynym typem, który obsługuje platforma tożsamości firmy Microsoft, jest `Bearer` . Aby uzyskać więcej informacji na temat tokenów okaziciela, zobacz [OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750) (Struktury autoryzacji OAuth 2.0: użycie tokenu okaziciela (RFC 6750).](https://www.rfc-editor.org/rfc/rfc6750.txt) |
| `scope` | Zakres dostępu udzielany w tokenie. |
| `expires_in` | Czas ważności tokenu dostępu (w sekundach). |
| `access_token` | Żądany token dostępu. Usługa wywołująca może używać tego tokenu do uwierzytelniania w usłudze odbieranej. |
| `refresh_token` | Token odświeżania dla żądanego tokenu dostępu. Usługa wywołująca może użyć tego tokenu, aby zażądać innego tokenu dostępu po wygaśnięciu bieżącego tokenu dostępu. Token odświeżania jest dostarczany tylko wtedy, gdy `offline_access` zażądano zakresu. |

### <a name="success-response-example"></a>Przykład odpowiedzi na pytanie o powodzenie

W poniższym przykładzie pokazano odpowiedź na żądanie tokenu dostępu dla internetowego interfejsu API w https://graph.microsoft.com odpowiedzi na żądanie.

```json
{
  "token_type": "Bearer",
  "scope": "https://graph.microsoft.com/user.read",
  "expires_in": 3269,
  "ext_expires_in": 0,
  "access_token": "eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkQ0NDYy0tY0hGa18wZE50MVEtc2loVzRMd2RwQVZISGpnTVdQZ0tQeVJIaGlDbUN2NkdyMEpmYmRfY1RmMUFxU21TcFJkVXVydVJqX3Nqd0JoN211eHlBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMzA1LCJuYmYiOjE0OTM5MzAzMDUsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQU9KYnFFWlRNTnEyZFcxYXpKN1RZMDlYeDdOT29EMkJEUlRWMXJ3b2ZRc1k9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJWR1ItdmtEZlBFQ2M1dWFDaENRSkFBIiwidmVyIjoiMS4wIn0.cubh1L2VtruiiwF8ut1m9uNBmnUJeYx4x0G30F7CqSpzHj1Sv5DCgNZXyUz3pEiz77G8IfOF0_U5A_02k-xzwdYvtJUYGH3bFISzdqymiEGmdfCIRKl9KMeoo2llGv0ScCniIhr2U1yxTIkIpp092xcdaDt-2_2q_ql1Ha_HtjvTV1f9XR3t7_Id9bR5BqwVX5zPO7JMYDVhUZRx08eqZcC-F3wi0xd_5ND_mavMuxe2wrpF-EZviO3yg0QVRr59tE3AoWl8lSGpVc97vvRCnp4WVRk26jJhYXFPsdk4yWqOKZqzr3IFGyD08WizD_vPSrXcCPbZP3XWaoTUKZSNJg",
  "refresh_token": "OAQABAAAAAABnfiG-mA6NTae7CdWW7QfdAALzDWjw6qSn4GUDfxWzJDZ6lk9qRw4An{a lot of characters here}"
}
```

Powyższy token dostępu to token w formacie v1.0 dla Microsoft Graph. Jest to spowodowane tym, że  format tokenu jest oparty na zasobie, do którego uzyskiwany jest dostęp, i niepowiązany z punktami końcowymi używanymi do żądania. Konfiguracja Microsoft Graph akceptowania tokenów w wersji 1.0, dzięki czemu platforma tożsamości firmy Microsoft tworzy tokeny dostępu w wersji 1.0, gdy klient żąda tokenów dla Microsoft Graph. Inne aplikacje mogą wskazywać, że chcą mieć tokeny w formacie 2.0, tokeny w wersji 1.0, a nawet zastrzeżone lub zaszyfrowane formaty tokenów.  Punkty końcowe w wersji 1.0 i 2.0 mogą emitować tokeny w obu formatach — w ten sposób zasób może zawsze uzyskać odpowiedni format tokenu niezależnie od sposobu lub miejsca, w którym klient zażądał tokenu. 

Tylko aplikacje powinny chcieć przyjrzeć się tokenom dostępu. Klienci **nie mogą ich** sprawdzać. Inspekcja tokenów dostępu dla innych aplikacji w kodzie spowoduje nieoczekiwane przerwanie aplikacji, gdy aplikacja zmieni format tokenów lub rozpocznie ich szyfrowanie. 

### <a name="error-response-example"></a>Przykład odpowiedzi na błąd

Punkt końcowy tokenu zwraca odpowiedź o błędzie podczas próby uzyskania tokenu dostępu dla podrzędnego interfejsu API, jeśli dla podrzędnego interfejsu API ustawiono zasady dostępu warunkowego (takie jak uwierzytelnianie wieloskładnikowe). [](../authentication/concept-mfa-howitworks.md) Usługa warstwy środkowej powinna udostępnić ten błąd aplikacji klienckiej, dzięki czemu aplikacja kliency może zapewnić interakcję użytkownika w celu spełnienia zasad dostępu warunkowego.

```json
{
    "error":"interaction_required",
    "error_description":"AADSTS50079: Due to a configuration change made by your administrator, or because you moved to a new location, you must enroll in multi-factor authentication to access 'bf8d80f9-9098-4972-b203-500f535113b1'.\r\nTrace ID: b72a68c3-0926-4b8e-bc35-3150069c2800\r\nCorrelation ID: 73d656cf-54b1-4eb2-b429-26d8165a52d7\r\nTimestamp: 2017-05-01 22:43:20Z",
    "error_codes":[50079],
    "timestamp":"2017-05-01 22:43:20Z",
    "trace_id":"b72a68c3-0926-4b8e-bc35-3150069c2800",
    "correlation_id":"73d656cf-54b1-4eb2-b429-26d8165a52d7",
    "claims":"{\"access_token\":{\"polids\":{\"essential\":true,\"values\":[\"9ab03e19-ed42-4168-b6b7-7001fb3e933a\"]}}}"
}
```

## <a name="use-the-access-token-to-access-the-secured-resource"></a>Uzyskiwanie dostępu do zabezpieczonego zasobu przy użyciu tokenu dostępu

Teraz usługa warstwy środkowej może użyć tokenu uzyskanego powyżej, aby wykonać uwierzytelnione żądania do podrzędnego internetowego interfejsu API, ustawiając token w `Authorization` nagłówku .

### <a name="example"></a>Przykład

```HTTP
GET /v1.0/me HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiO ... 0X2tnSQLEANnSPHY0gKcgw
```

## <a name="saml-assertions-obtained-with-an-oauth20-obo-flow"></a>Asercji SAML uzyskanych za pomocą przepływu OBO OAuth 2.0

Niektóre usługi internetowe oparte na uwierzytelnieniu OAuth muszą uzyskać dostęp do innych interfejsów API usługi internetowej, które akceptują asercji SAML w przepływach nieinterakcyjnych. Azure Active Directory może zapewnić asercja SAML w odpowiedzi na przepływ On-Behalf-Of, który używa usługi internetowej opartej na saml jako zasobu docelowego.

Jest to standardowe rozszerzenie przepływu OAuth 2.0 On-Behalf-Of, które umożliwia aplikacji opartej na standardzie OAuth2 dostęp do punktów końcowych interfejsu API usługi internetowej, które zużywają tokeny SAML.

> [!TIP]
> Wywołując usługę internetową chronioną przez saml z poziomu aplikacji internetowej frontonu, możesz po prostu wywołać interfejs API i zainicjować normalny przepływ uwierzytelniania interaktywnego z istniejącą sesją użytkownika. PrzepływU OBO należy używać tylko wtedy, gdy wywołanie typu usługa do usługi wymaga tokenu SAML w celu zapewnienia kontekstu użytkownika.
 
 ### <a name="obtain-a-saml-token-by-using-an-obo-request-with-a-shared-secret"></a>Uzyskiwanie tokenu SAML przy użyciu żądania OBO z udostępnionym kluczem tajnym

Żądanie service-to-service dla asercji SAML zawiera następujące parametry:

| Parametr | Typ | Opis |
| --- | --- | --- |
| grant_type |wymagane | Typ żądania tokenu. W przypadku żądania, które używa JWT, wartość musi być **urn:ietf:params:oauth:grant-type:jwt-bearer**. |
| Potwierdzenia |wymagane | Wartość tokenu dostępu używanego w żądaniu.|
| client_id |wymagane | Identyfikator aplikacji przypisany do usługi wywołującej podczas rejestracji w usłudze Azure AD. Aby znaleźć identyfikator aplikacji w Azure Portal, wybierz pozycję **Active Directory,** wybierz katalog, a następnie wybierz nazwę aplikacji. |
| client_secret |wymagane | Klucz zarejestrowany dla usługi wywołującej w usłudze Azure AD. Ta wartość powinna zostać zanotowana w czasie rejestracji. |
| zasób |wymagane | Identyfikator URI aplikacji usługi odbieranej (zabezpieczony zasób). Jest to zasób, który będzie odbiorcą tokenu SAML. Aby znaleźć identyfikator URI aplikacji w Azure Portal, wybierz pozycję **Active Directory** i wybierz katalog. Wybierz nazwę aplikacji, wybierz pozycję **Wszystkie ustawienia,** a następnie wybierz pozycję **Właściwości.** |
| requested_token_use |wymagane | Określa sposób przetwarzania żądania. W przepływie On-Behalf-Of wartość musi być **on_behalf_of**. |
| requested_token_type | wymagane | Określa typ żądanego tokenu. Wartość może być **urn:ietf:params:oauth:token-type:saml2** lub **urn:ietf:params:oauth:token-type:saml1** w zależności od wymagań zasobu, do którego uzyskano dostęp. |

Odpowiedź zawiera token SAML zakodowany w formacie UTF8 i Base64url.

- **SubjectConfirmationData** dla asercji SAML pochodząca z wywołania OBO: jeśli aplikacja docelowa wymaga wartości odbiorcy w **subjectConfirmationData,** wartość musi być adresem URL odpowiedzi bez symboli wieloznacznych w konfiguracji aplikacji zasobów.
- **Węzeł SubjectConfirmationData:** węzeł nie może zawierać atrybutu **InResponseTo,** ponieważ nie jest częścią odpowiedzi SAML. Aplikacja odbieraca token SAML musi być w stanie zaakceptować asercja SAML bez atrybutu **InResponseTo.**

- **Zgoda:** aby otrzymać token SAML zawierający dane użytkownika w przepływie protokołu OAuth, należy wyrazić zgodę. Aby uzyskać informacje na temat uprawnień i uzyskiwania zgody administratora, zobacz Uprawnienia i zgody w [Azure Active Directory 1.0.](https://docs.microsoft.com/azure/active-directory/azuread-dev/v1-permissions-consent)

### <a name="response-with-saml-assertion"></a>Odpowiedź z asercją SAML

| Parametr | Opis |
| --- | --- |
| token_type |Wskazuje wartość typu tokenu. Jedynym typem, który obsługuje usługa Azure AD, jest **Bearer.** Aby uzyskać więcej informacji na temat tokenów okaziciela, zobacz [OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750) (OAuth 2.0 Authorization Framework: użycie tokenu bearer (RFC 6750) ).](https://www.rfc-editor.org/rfc/rfc6750.txt) |
| scope |Zakres dostępu udzielany w tokenie. |
| expires_in |Czas ważności tokenu dostępu (w sekundach). |
| expires_on |Godzina wygaśnięcia tokenu dostępu. Data jest reprezentowana jako liczba sekund od 1970-01-01T0:0:0Z UTC do czasu wygaśnięcia. Ta wartość służy do określania okresu istnienia buforowanych tokenów. |
| zasób |Identyfikator URI aplikacji usługi odbieranej (zabezpieczony zasób). |
| access_token |Parametr, który zwraca asercja SAML. |
| refresh_token |Token odświeżania. Usługa wywołująca może użyć tego tokenu, aby zażądać innego tokenu dostępu po wygaśnięciu bieżącej asercji SAML. |

- token_type: Bearer
- expires_in: 3296
- ext_expires_in: 0
- expires_on: 1529627844
- Zasobów: `https://api.contoso.com`
- access_token: \<SAML assertion\>
- issued_token_type: urn:ietf:params:oauth:token-type:saml2
- refresh_token: \<Refresh token\>


## <a name="gaining-consent-for-the-middle-tier-application"></a>Uzyskiwanie zgody dla aplikacji warstwy środkowej

W zależności od architektury lub użycia aplikacji można rozważyć różne strategie zapewniające powodzenie przepływu OBO. We wszystkich przypadkach ostatecznym celem jest zapewnienie, że zostanie udzielona właściwa zgoda, dzięki czemu aplikacja kliency będzie w stanie wywołać aplikację warstwy środkowej, a aplikacja warstwy środkowej będzie mieć uprawnienia do wywołania zasobu back-end.

> [!NOTE]
> Wcześniej system konto Microsoft (konta osobiste) nie obsługiał pola "Znana aplikacja klienska" ani nie mógł wyświetlać połączonej zgody.  Zostało to dodane i wszystkie aplikacje na platformie tożsamości firmy Microsoft mogą używać znanego podejścia aplikacji klienckiej do uzyskiwania zgody dla wywołań OBO.

### <a name="default-and-combined-consent"></a>/.default i łączna zgoda

Aplikacja warstwy środkowej dodaje klienta do listy znanych aplikacji klienckich w manifeście, a następnie może wyzwolić połączony przepływ zgody dla siebie i aplikacji warstwy środkowej. Na platformie tożsamości firmy Microsoft odbywa się to przy użyciu [ `/.default` zakresu](v2-permissions-and-consent.md#the-default-scope). Podczas wyzwalania ekranu zgody przy użyciu znanych aplikacji klienckich i na ekranie zgody wyświetlane są uprawnienia klienta do interfejsu API warstwy środkowej, a także wszelkie uprawnienia wymagane przez interfejs API warstwy `/.default` środkowej.  Użytkownik wyraża zgodę dla obu aplikacji, a przepływ OBO działa.

### <a name="pre-authorized-applications"></a>Wstępnie autoryzowane aplikacje

Zasoby mogą wskazywać, że aplikacja zawsze ma uprawnienia do odbierania określonych zakresów. Jest to przydatne przede wszystkim w celu sprawnszego połączenia między klientem frontonie a zasobem frontoni. Zasób może zadeklarować wiele wstępnie autoryzowanych aplikacji — każda taka aplikacja może zażądać tych uprawnień w przepływie OBO i odebrać je bez zgody użytkownika.

### <a name="admin-consent"></a>zgoda administratora

Administrator dzierżawy może zagwarantować, że aplikacje mają uprawnienia do wywołania wymaganych interfejsów API, udzielając zgody administratora dla aplikacji warstwy środkowej. W tym celu administrator może znaleźć aplikację warstwy środkowej w swojej dzierżawie, otworzyć stronę wymaganych uprawnień i wybrać opcję nadawać uprawnienia aplikacji. Aby dowiedzieć się więcej na temat zgody administratora, zobacz [dokumentację dotyczącą wyrażania zgody i uprawnień.](v2-permissions-and-consent.md)

### <a name="use-of-a-single-application"></a>Korzystanie z jednej aplikacji

W niektórych scenariuszach może być tylko jedno parowanie klienta warstwy środkowej i frontonia. W tym scenariuszu może być łatwiej uczynić tę pojedynczą aplikację, całkowicie negując potrzebę zastosowania aplikacji warstwy środkowej. Aby uwierzytelnić się między frontonem i internetowym interfejsem API, możesz użyć plików cookie, id_token lub tokenu dostępu żądanego dla samej aplikacji. Następnie zażądaj zgody z tej pojedynczej aplikacji na zasób back-end.

## <a name="client-limitations"></a>Ograniczenia klienta

Jeśli klient używa niejawnego przepływu w celu uzyskania id_token, a klient ma również symbole wieloznaczne w adresie URL odpowiedzi, id_token nie może być używany dla przepływu OBO.  Jednak tokeny dostępu pozyskane za pośrednictwem niejawnego przepływu udzielania mogą być nadal dostępne dla poufnego klienta, nawet jeśli klient inicjujący ma zarejestrowany adres URL odpowiedzi z symbolami wieloznacznymi.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat protokołu OAuth 2.0 i innego sposobu uwierzytelniania między usługami przy użyciu poświadczeń klienta.

* [Przyznawanie poświadczeń klienta OAuth 2.0 na platformie tożsamości firmy Microsoft](v2-oauth2-client-creds-grant-flow.md)
* [Przepływ kodu OAuth 2.0 na platformie tożsamości firmy Microsoft](v2-oauth2-auth-code-flow.md)
* [Korzystanie z `/.default` zakresu](v2-permissions-and-consent.md#the-default-scope)
