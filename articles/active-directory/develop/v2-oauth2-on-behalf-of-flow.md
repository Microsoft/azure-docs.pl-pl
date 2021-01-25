---
title: Microsoft Identity platform i OAuth 2.0 w imieniu przepływu | Azure
titleSuffix: Microsoft identity platform
description: W tym artykule opisano sposób korzystania z komunikatów HTTP w celu zaimplementowania uwierzytelniania usługi do usługi przy użyciu przepływu uwierzytelniania OAuth 2.0 w imieniu użytkownika.
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
ms.openlocfilehash: ff8e03b813e2cb890192667e3466d920eaabc72c
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98756091"
---
# <a name="microsoft-identity-platform-and-oauth-20-on-behalf-of-flow"></a>Platforma tożsamości firmy Microsoft i protokół OAuth 2,0 w imieniu użytkownika


Przepływ uwierzytelniania OAuth 2,0 w imieniu (OBO) obsługuje przypadek użycia, w którym aplikacja wywołuje usługę/interfejs API sieci Web, co z kolei musi wywołać inną usługę/internetowy interfejs API. Pomysłem jest propagowanie tożsamości i uprawnień delegowanych użytkowników za pomocą łańcucha żądań. W przypadku usługi warstwy środkowej aby żądania były uwierzytelniane w usłudze podrzędnej, należy zabezpieczyć token dostępu z platformy tożsamości firmy Microsoft w imieniu użytkownika.

W tym artykule opisano, jak programować bezpośrednio w odniesieniu do protokołu w aplikacji.  Jeśli to możliwe, zalecamy korzystanie z obsługiwanych bibliotek uwierzytelniania firmy Microsoft (MSAL) zamiast [uzyskiwać tokeny i wywoływać zabezpieczone interfejsy API sieci Web](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Zapoznaj się również z [przykładowymi aplikacjami korzystającymi z MSAL](sample-v2-code.md).


Od maja 2018 niektóre pochodne niejawnego przepływu `id_token` nie mogą być używane dla przepływu OBO. Aplikacje jednostronicowe (aplikacji jednostronicowych) powinny przekazać token **dostępu** do klienta poufnego w warstwie środkowej, aby w zamian wykonać przepływy OBO. Aby uzyskać więcej informacji o tym, którzy klienci mogą wykonywać wywołania OBO, zobacz [ograniczenia](#client-limitations).

## <a name="protocol-diagram"></a>Diagram protokołu

Załóżmy, że użytkownik został uwierzytelniony w aplikacji przy użyciu [przepływu przyznawania kodu autoryzacji OAuth 2,0](v2-oauth2-auth-code-flow.md) lub innego przepływu logowania. W tym momencie aplikacja ma token dostępu *dla interfejsu API a* (token a) z oświadczeniami użytkownika i wyraża zgodę na dostęp do internetowego interfejsu API sieci Web (API A). Teraz interfejs API A musi wykonać uwierzytelnione żądanie do podrzędnego interfejsu API sieci Web (API B).

Kroki, które należy wykonać, stanowią "OBO Flow" i objaśniono na poniższym diagramie.

![Pokazuje przepływ protokołu OAuth 2.0 w imieniu użytkownika](./media/v2-oauth2-on-behalf-of-flow/protocols-oauth-on-behalf-of-flow.png)

1. Aplikacja kliencka wysyła żądanie do interfejsu API A z tokenem A (z `aud` zastrzeżeniem interfejsu API a).
1. Interfejs API A uwierzytelnia się w punkcie końcowym wystawiania tokenów platformy tożsamości firmy Microsoft i żąda tokenu w celu uzyskania dostępu do interfejsu API B.
1. Punkt końcowy wystawiania tokenów platformy tożsamości firmy Microsoft sprawdza poprawność poświadczeń interfejsu API A wraz z tokenem a i wystawia token dostępu dla interfejsu API B (token B) w interfejsie API A.
1. Token B jest ustawiany przez interfejs API A w nagłówku autoryzacji żądania do interfejsu API B.
1. Dane z zabezpieczonego zasobu są zwracane przez interfejs API B do interfejsu API A, a następnie do klienta.

W tym scenariuszu Usługa warstwy środkowej nie ma interakcji z użytkownikiem, aby uzyskać zgodę użytkownika na dostęp do podrzędnego interfejsu API. W związku z tym, opcja udzielenia dostępu do podrzędnego interfejsu API jest przedstawiona jako część kroku wyrażania zgody podczas uwierzytelniania. Aby dowiedzieć się, jak skonfigurować to ustawienie dla aplikacji, zobacz temat [Uzyskiwanie zgody na aplikację warstwy środkowej](#gaining-consent-for-the-middle-tier-application).

## <a name="middle-tier-access-token-request"></a>Żądanie tokenu dostępu warstwy środkowej

Aby zażądać tokenu dostępu, wprowadź wpis HTTP do punktu końcowego tokenu platformy tożsamości firmy Microsoft dla dzierżawy z następującymi parametrami.

```
https://login.microsoftonline.com/<tenant>/oauth2/v2.0/token
```

Istnieją dwa przypadki, w zależności od tego, czy aplikacja kliencka ma być zabezpieczona przez wspólny klucz tajny czy certyfikat.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Pierwszy przypadek: żądanie tokenu dostępu przy użyciu wspólnego klucza tajnego

Gdy jest używany wspólny klucz tajny, żądanie tokenu dostępu między usługami zawiera następujące parametry:

| Parametr | Typ | Opis |
| --- | --- | --- |
| `grant_type` | Wymagane | Typ żądania tokenu. W przypadku żądania przy użyciu tokenu JWT wartość musi być równa `urn:ietf:params:oauth:grant-type:jwt-bearer` . |
| `client_id` | Wymagane | Identyfikator aplikacji (klienta), którą strona [Rejestracje aplikacji Azure Portala](https://go.microsoft.com/fwlink/?linkid=2083908) została przypisana do aplikacji. |
| `client_secret` | Wymagane | Wpis tajny klienta wygenerowany dla aplikacji na stronie Azure Portal-Rejestracje aplikacji. |
| `assertion` | Wymagane | Token dostępu, który został wysłany do interfejsu API warstwy środkowej.  Token musi mieć następującą liczbę odbiorców ( `aud` ) aplikacji tworzących żądanie OBO (aplikację, która jest oznaczona przez `client-id` pole). Aplikacje nie mogą wykonać tokenu dla innej aplikacji (np. Jeśli klient wysyła do interfejsu API token przeznaczony dla programu MS Graph, interfejs API nie może go zrealizować przy użyciu OBO.  Zamiast tego należy odrzucić token).  |
| `scope` | Wymagane | Rozdzielana spacjami lista zakresów dla żądania tokenu. Aby uzyskać więcej informacji, zobacz [zakresy](v2-permissions-and-consent.md). |
| `requested_token_use` | Wymagane | Określa, w jaki sposób żądanie powinno być przetwarzane. W przepływie OBO wartość musi być ustawiona na `on_behalf_of` . |

#### <a name="example"></a>Przykład

Następujące HTTP POST żąda tokenu dostępu i tokenu odświeżania z `user.read` zakresem dla internetowego https://graph.microsoft.com interfejsu API.

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

Żądanie tokenu dostępu między usługami i certyfikatem zawiera następujące parametry:

| Parametr | Typ | Opis |
| --- | --- | --- |
| `grant_type` | Wymagane | Typ żądania tokenu. W przypadku żądania przy użyciu tokenu JWT wartość musi być równa `urn:ietf:params:oauth:grant-type:jwt-bearer` . |
| `client_id` | Wymagane |  Identyfikator aplikacji (klienta), którą strona [Rejestracje aplikacji Azure Portala](https://go.microsoft.com/fwlink/?linkid=2083908) została przypisana do aplikacji. |
| `client_assertion_type` | Wymagane | Wartość musi być `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` . |
| `client_assertion` | Wymagane | Potwierdzenie (token sieci Web JSON), które należy utworzyć i podpisać przy użyciu certyfikatu zarejestrowanego jako poświadczenia dla aplikacji. Aby dowiedzieć się, jak zarejestrować certyfikat i format potwierdzenia, zobacz [poświadczenia certyfikatu](active-directory-certificate-credentials.md). |
| `assertion` | Wymagane |  Token dostępu, który został wysłany do interfejsu API warstwy środkowej.  Token musi mieć następującą liczbę odbiorców ( `aud` ) aplikacji tworzących żądanie OBO (aplikację, która jest oznaczona przez `client-id` pole). Aplikacje nie mogą wykonać tokenu dla innej aplikacji (np. Jeśli klient wysyła do interfejsu API token przeznaczony dla programu MS Graph, interfejs API nie może go zrealizować przy użyciu OBO.  Zamiast tego należy odrzucić token).  |
| `requested_token_use` | Wymagane | Określa, w jaki sposób żądanie powinno być przetwarzane. W przepływie OBO wartość musi być ustawiona na `on_behalf_of` . |
| `scope` | Wymagane | Rozdzielana spacjami lista zakresów dla żądania tokenu. Aby uzyskać więcej informacji, zobacz [zakresy](v2-permissions-and-consent.md).|

Zwróć uwagę, że parametry są prawie takie same, jak w przypadku żądania przez wspólny klucz tajny, z tą różnicą, że `client_secret` parametr jest zastępowany przez dwa parametry: a `client_assertion_type` i `client_assertion` .

#### <a name="example"></a>Przykład

Następujące HTTP POST żąda tokenu dostępu z `user.read` zakresem dla internetowego https://graph.microsoft.com interfejsu API z certyfikatem.

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

## <a name="middle-tier-access-token-response"></a>Odpowiedź na token dostępu warstwy środkowej

Odpowiedź sukcesu to odpowiedź OAuth 2,0 JSON z poniższymi parametrami.

| Parametr | Opis |
| --- | --- |
| `token_type` | Wskazuje wartość typu tokenu. Jedynym typem, który obsługuje platforma tożsamości firmy Microsoft, jest `Bearer` . Aby uzyskać więcej informacji o tokenach okaziciela, zobacz [Framework uwierzytelniania OAuth 2,0: użycie tokenu okaziciela (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| `scope` | Zakres dostępu udzielony w tokenie. |
| `expires_in` | Czas (w sekundach), przez jaki token dostępu jest prawidłowy. |
| `access_token` | Żądany token dostępu. Usługa wywołująca może używać tego tokenu do uwierzytelniania w usłudze odbiorczej. |
| `refresh_token` | Token odświeżania dla żądanego tokenu dostępu. Usługa wywołująca może używać tego tokenu do żądania innego tokenu dostępu po wygaśnięciu bieżącego tokenu dostępu. Token odświeżania jest podany tylko w przypadku, gdy `offline_access` żądany został zakres. |

### <a name="success-response-example"></a>Przykład odpowiedzi sukcesu

Poniższy przykład przedstawia Pomyślne odpowiedzi na żądanie tokenu dostępu dla internetowego https://graph.microsoft.com interfejsu API.

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

Powyższy token dostępu jest tokenem w formacie v 1.0 dla Microsoft Graph. Jest to spowodowane tym, że format tokenu jest oparty na **zasobach** , do których uzyskuje się dostęp i które nie są powiązane z punktami końcowymi używanymi do żądania. Microsoft Graph jest skonfigurowany do akceptowania tokenów v 1.0, więc platforma tożsamości firmy Microsoft generuje tokeny dostępu w wersji 1.0, gdy klient żąda tokenów dla Microsoft Graph. Inne aplikacje mogą wskazywać, że chcą mieć tokeny w formacie v 2.0, tokeny w formacie v 1.0, a nawet zastrzeżone lub zaszyfrowane formaty tokenów.  Punkty końcowe v 1.0 i v 2.0 mogą emitować dowolny format tokenu — w ten sposób zasób zawsze uzyskuje odpowiedni format tokenu niezależnie od tego, czy token został żądany przez klienta. 

Tylko aplikacje powinny przeglądać tokeny dostępu. Klienci **nie muszą** ich sprawdzać. Sprawdzanie tokenów dostępu dla innych aplikacji w kodzie spowoduje nieoczekiwane przerwanie działania aplikacji, gdy aplikacja zmieni format ich tokenów lub zacznie je szyfrować. 

### <a name="error-response-example"></a>Przykład odpowiedzi na błąd

W przypadku próby uzyskania tokenu dostępu dla podrzędnego interfejsu API zwracany jest komunikat o błędzie, jeśli w przypadku interfejsu API podrzędnego jest ustawiona zasada dostępu warunkowego (na przykład [uwierzytelnianie wieloskładnikowe](../authentication/concept-mfa-howitworks.md)). Usługa warstwy środkowej powinna wystawić ten błąd w aplikacji klienckiej, dzięki czemu aplikacja kliencka może zapewnić interakcję użytkownika w celu spełnienia zasad dostępu warunkowego.

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

## <a name="use-the-access-token-to-access-the-secured-resource"></a>Korzystanie z tokenu dostępu w celu uzyskania dostępu do zabezpieczonego zasobu

Teraz usługa warstwy środkowej może użyć tokenu pozyskanego powyżej, aby wykonać uwierzytelnione żądania do podrzędnego interfejsu API sieci Web, ustawiając token w `Authorization` nagłówku.

### <a name="example"></a>Przykład

```HTTP
GET /v1.0/me HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiO ... 0X2tnSQLEANnSPHY0gKcgw
```

## <a name="saml-assertions-obtained-with-an-oauth20-obo-flow"></a>Potwierdzenia SAML uzyskane przy użyciu przepływu OBO protokołu OAuth 2.0

Niektóre usługi sieci Web oparte na protokole OAuth muszą uzyskiwać dostęp do innych interfejsów API usługi sieci Web, które akceptują potwierdzenia SAML w nieinteraktywnych przepływach. Azure Active Directory może zapewnić potwierdzenie SAML w odpowiedzi na w imieniu przepływu, który używa usługi sieci Web opartej na protokole SAML jako zasobu docelowego.

Jest to niestandardowe rozszerzenie dla przepływu OAuth 2,0 w imieniu użytkownika, które umożliwia aplikacji opartej na OAuth2 dostęp do punktów końcowych interfejsu API usługi sieci Web, które używają tokenów SAML.

> [!TIP]
> Po wywołaniu usługi sieci Web chronionej przy użyciu protokołu SAML z aplikacji sieci Web frontonu można po prostu wywołać interfejs API i zainicjować normalny przepływ uwierzytelniania interaktywnego za pomocą istniejącej sesji użytkownika. Należy używać przepływu OBO tylko wtedy, gdy wywołanie usługi Service to Service wymaga tokenu języka SAML do zapewnienia kontekstu użytkownika.

## <a name="gaining-consent-for-the-middle-tier-application"></a>Uzyskanie zgody na aplikację warstwy środkowej

W zależności od architektury lub użycia aplikacji można wziąć pod uwagę różne strategie, aby upewnić się, że przepływ OBO zakończył się pomyślnie. We wszystkich przypadkach ostatecznym celem jest zapewnienie odpowiedniej zgody, dzięki czemu aplikacja kliencka może wywołać aplikację warstwy środkowej, a aplikacja warstwy środkowej ma uprawnienia do wywołania zasobu zaplecza.

> [!NOTE]
> Wcześniej system konto Microsoft (konta osobiste) nie obsługiwał pola "znana aplikacja kliencka" ani nie może wyświetlić połączonej zgody.  Zostało to dodane, a wszystkie aplikacje na platformie tożsamości firmy Microsoft mogą używać znanego podejścia aplikacji klienta do uzyskania zgody na wywołania OBO.

### <a name="default-and-combined-consent"></a>/.default i połączona zgoda

Aplikacja warstwy środkowej dodaje klienta do listy znanych aplikacji klienckich w jego manifeście, a następnie klient może wyzwolić połączony przepływ zgody zarówno dla siebie, jak i dla aplikacji warstwy środkowej. Na platformie tożsamości firmy Microsoft odbywa się to przy użyciu [ `/.default` zakresu](v2-permissions-and-consent.md#the-default-scope). Gdy Wyzwalasz ekran zgody przy użyciu znanych aplikacji klienckich, a `/.default` na ekranie wyrażania zgody zostaną wyświetlone uprawnienia **zarówno** dla klienta, jak i do interfejsu API warstwy środkowej, a także Zażądaj uprawnień wymaganych przez interfejs API warstwy środkowej. Użytkownik wyraża zgodę na obie aplikacje, a następnie przepływ OBO działa.

### <a name="pre-authorized-applications"></a>Wstępnie autoryzowane aplikacje

Zasoby mogą wskazywać, że dana aplikacja zawsze ma uprawnienia do odbierania określonych zakresów. Jest to szczególnie przydatne w przypadku, gdy połączenia między klientem frontonu i zasobem zaplecza są bardziej bezproblemowe. Zasób może deklarować wiele wstępnie autoryzowanych aplikacji — każda taka aplikacja może zażądać tych uprawnień w przepływie OBO i odbierać je bez zgody użytkownika.

### <a name="admin-consent"></a>zgoda administratora

Administrator dzierżawy może zagwarantować, że aplikacje mają uprawnienia do wywoływania wymaganych interfejsów API, zapewniając zgodę administratora na aplikację warstwy środkowej. Aby to zrobić, administrator może znaleźć aplikację warstwy środkowej w swojej dzierżawie, otworzyć stronę wymagane uprawnienia i wybrać opcję udzielenia uprawnienia do aplikacji. Aby dowiedzieć się więcej na temat zgody administratora, zapoznaj się z [dokumentacją dotyczącą zgody i uprawnień](v2-permissions-and-consent.md).

### <a name="use-of-a-single-application"></a>Korzystanie z pojedynczej aplikacji

W niektórych scenariuszach może istnieć tylko jedno skojarzenie klienta warstwy środkowej i frontonu. W tym scenariuszu może być łatwiejsze tworzenie tej pojedynczej aplikacji, co wyklucza konieczność całkowitego zastosowania aplikacji warstwy środkowej. Aby przeprowadzić uwierzytelnianie między frontonem a interfejsem API sieci Web, można użyć plików cookie, id_token lub tokenu dostępu żądanego dla aplikacji. Następnie Zażądaj zgody tej pojedynczej aplikacji na zasób zaplecza.

## <a name="client-limitations"></a>Ograniczenia klienta

Jeśli klient używa niejawnego przepływu do uzyskania id_token, a ten klient ma także symbole wieloznaczne w adresie URL odpowiedzi, nie można użyć id_token dla przepływu OBO.  Jednak tokeny dostępu nabyte za pomocą niejawnego przepływu dotacji nadal mogą być realizowane przez poufnego klienta, nawet jeśli klient inicjujący ma zarejestrowany adres URL odpowiedzi z symbolem wieloznacznym.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o protokole OAuth 2,0 i innym sposobie przeprowadzania uwierzytelniania usługi przy użyciu poświadczeń klienta.

* [Przyznawanie poświadczeń klienta OAuth 2,0 na platformie tożsamości firmy Microsoft](v2-oauth2-client-creds-grant-flow.md)
* [Przepływ kodu OAuth 2,0 na platformie tożsamości firmy Microsoft](v2-oauth2-auth-code-flow.md)
* [Korzystanie z `/.default` zakresu](v2-permissions-and-consent.md#the-default-scope)
