---
title: Platforma tożsamości firmy Microsoft i przepływ OAuth2.0 w imieniu | Azure
description: W tym artykule opisano sposób używania wiadomości HTTP do implementowania usługi do uwierzytelniania usługi przy użyciu przepływu W imieniu użytkownika OAuth2.0.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 1/3/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 3d3e071d5f2f181f5b17e79f2f1097394d0ebaf3
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868436"
---
# <a name="microsoft-identity-platform-and-oauth-20-on-behalf-of-flow"></a>Platforma tożsamości firmy Microsoft i przepływ OAuth 2.0 w imieniu


OAuth 2.0 On-Behalf-Of flow (OBO) służy przypadku użycia, gdy aplikacja wywołuje interfejs API usługi/sieci web, który z kolei musi wywołać inną usługę/interfejs API sieci web. Chodzi o propagowanie tożsamości delegowanego użytkownika i uprawnień za pośrednictwem łańcucha żądań. Aby usługa warstwy środkowej była zabezpieczona do usługi podrzędnej, musi zabezpieczyć token dostępu z platformy tożsamości firmy Microsoft w imieniu użytkownika.

W tym artykule opisano sposób programowania bezpośrednio względem protokołu w aplikacji.  Jeśli to możliwe, zaleca się użycie obsługiwanych bibliotek uwierzytelniania firmy Microsoft (MSAL) zamiast tego do [uzyskiwania tokenów i wywoływania zabezpieczonych interfejsów API sieci Web](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Zapoznaj się również z [przykładowymi aplikacjami korzystającymi z programu MSAL](sample-v2-code.md).

> [!NOTE]
> Od maja 2018 r. niektóre `id_token` pochodzące przepływ niejawny nie mogą być używane dla przepływu OBO. Aplikacje jednostronicowe (SPA) należy przekazać token **dostępu** do klienta poufnego warstwy środkowej do wykonywania przepływów OBO zamiast tego. Aby uzyskać więcej informacji o tym, którzy klienci mogą wykonywać wywołania OBO, zobacz [ograniczenia](#client-limitations).

## <a name="protocol-diagram"></a>Diagram protokołu

Załóżmy, że użytkownik został uwierzytelniony w aplikacji przy użyciu [kodu autoryzacji OAuth 2.0 udzielić przepływu](v2-oauth2-auth-code-flow.md) lub innego przepływu logowania. W tym momencie aplikacja ma token dostępu *dla interfejsu API A* (token A) z oświadczeń użytkownika i zgody na dostęp do interfejsu API sieci web warstwy środkowej (API A). Teraz interfejs API A musi wykonać uwierzytelnione żądanie do podrzędnego interfejsu API sieci web (API B).

Kroki, które należy wykonać stanowią przepływ OBO i są wyjaśnione za pomocą poniższego diagramu.

![Pokazuje przepływ OAuth2.0 w imieniu](./media/v2-oauth2-on-behalf-of-flow/protocols-oauth-on-behalf-of-flow.png)

1. Aplikacja kliencka sprawia, że żądanie do `aud` interfejsu API A z tokenem A (z oświadczeniem interfejsu API A).
1. Interfejs API A uwierzytelnia się w punkcie końcowym wydawania tokenu platformy tożsamości firmy Microsoft i żąda tokenu dostępu do interfejsu API B.
1. Punkt końcowy wystawiania tokenów platformy tożsamości firmy Microsoft sprawdza poprawność poświadczeń interfejsu API A wraz z tokenem A i wystawia token dostępu dla interfejsu API B (token B) do interfejsu API A.
1. Token B jest ustawiany przez interfejs API A w nagłówku autoryzacji żądania do interfejsu API B.
1. Dane z zabezpieczonego zasobu są zwracane przez interfejs API B do interfejsu API A, a stamtąd do klienta.

> [!NOTE]
> W tym scenariuszu usługa warstwy środkowej nie ma interakcji użytkownika, aby uzyskać zgodę użytkownika na dostęp do interfejsu API podrzędnego. W związku z tym opcja udzielenia dostępu do interfejsu API podrzędnego jest przedstawiona z góry jako część kroku zgody podczas uwierzytelniania. Aby dowiedzieć się, jak skonfigurować tę aplikację, zobacz [Uzyskiwanie zgody na aplikację warstwy środkowej.](#gaining-consent-for-the-middle-tier-application)

## <a name="service-to-service-access-token-request"></a>Żądanie tokenu dostępu usługi do usługi

Aby zażądać tokenu dostępu, należy wprowadzić wpis HTTP do punktu końcowego tokenu platformy tożsamości specyficznej dla dzierżawy firmy Microsoft z następującymi parametrami.

```
https://login.microsoftonline.com/<tenant>/oauth2/v2.0/token
```

Istnieją dwa przypadki w zależności od tego, czy aplikacja kliencka zdecyduje się być zabezpieczona przez wspólny klucz tajny lub certyfikat.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Pierwszy przypadek: żądanie tokenu dostępu z udostępnionym kluczem tajnym

Podczas korzystania z udostępnionego klucza tajnego żądanie tokenu dostępu usługi do usługi zawiera następujące parametry:

| Parametr |  | Opis |
| --- | --- | --- |
| `grant_type` | Wymagany | Typ żądania tokenu. Dla żądania przy użyciu JWT, `urn:ietf:params:oauth:grant-type:jwt-bearer`wartość musi być . |
| `client_id` | Wymagany | Identyfikator aplikacji (klienta), który strona [azure portal — rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) została przypisana do aplikacji. |
| `client_secret` | Wymagany | Klucz tajny klienta wygenerowany dla aplikacji na stronie Azure portal — rejestracje aplikacji. |
| `assertion` | Wymagany | Wartość tokenu używanego w żądaniu.  Ten token musi mieć odbiorców aplikacji, która tego żądania OBO `client-id` (aplikacja oznaczona przez pole). |
| `scope` | Wymagany | Przestrzeń oddzielona listą zakresów dla żądania tokenu. Aby uzyskać więcej informacji, zobacz [zakresy](v2-permissions-and-consent.md). |
| `requested_token_use` | Wymagany | Określa sposób przetwarzania żądania. W przepływie OBO wartość musi `on_behalf_of`być ustawiona na . |

#### <a name="example"></a>Przykład

Następujący protokół HTTP POST żąda tokenu `user.read` dostępu https://graph.microsoft.com i odświeżania tokenu z zakresem dla internetowego interfejsu API.

```HTTP
//line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn:ietf:params:oauth:grant-type:jwt-bearer
&client_id=2846f71b-a7a4-4987-bab3-760035b2f389
&client_secret=BYyVnAt56JpLwUcyo47XODd
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE0OTM5MjA5MTYsIm5iZiI6MTQ5MzkyMDkxNiwiZXhwIjoxNDkzOTI0ODE2LCJhaW8iOiJBU1FBMi84REFBQUFnZm8vNk9CR0NaaFV2NjJ6MFFYSEZKR0VVYUIwRUlIV3NhcGducndMMnVrPSIsIm5hbWUiOiJOYXZ5YSBDYW51bWFsbGEiLCJvaWQiOiJkNWU5NzljNy0zZDJkLTQyYWYtOGYzMC03MjdkZDRjMmQzODMiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJuYWNhbnVtYUBtaWNyb3NvZnQuY29tIiwic3ViIjoiZ1Q5a1FMN2hXRUpUUGg1OWJlX1l5dVZNRDFOTEdiREJFWFRhbEQzU3FZYyIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInV0aSI6IjN5U3F4UHJweUVPd0ZsTWFFMU1PQUEiLCJ2ZXIiOiIyLjAifQ.TPPJSvpNCSCyUeIiKQoLMixN1-M-Y5U0QxtxVkpepjyoWNG0i49YFAJC6ADdCs5nJXr6f-ozIRuaiPzy29yRUOdSz_8KqG42luCyC1c951HyeDgqUJSz91Ku150D9kP5B9-2R-jgCerD_VVuxXUdkuPFEl3VEADC_1qkGBiIg0AyLLbz7DTMp5DvmbC09DhrQQiouHQGFSk2TPmksqHm3-b3RgeNM1rJmpLThis2ZWBEIPx662pjxL6NJDmV08cPVIcGX4KkFo54Z3rfwiYg4YssiUc4w-w3NJUBQhnzfTl4_Mtq2d7cVlul9uDzras091vFy32tWkrpa970UvdVfQ
&scope=https://graph.microsoft.com/user.read+offline_access
&requested_token_use=on_behalf_of
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Drugi przypadek: żądanie tokenu dostępu z certyfikatem

Żądanie tokenu dostępu usługi do usługi z certyfikatem zawiera następujące parametry:

| Parametr |  | Opis |
| --- | --- | --- |
| `grant_type` | Wymagany | Typ żądania tokenu. Dla żądania przy użyciu JWT, `urn:ietf:params:oauth:grant-type:jwt-bearer`wartość musi być . |
| `client_id` | Wymagany |  Identyfikator aplikacji (klienta), który strona [azure portal — rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) została przypisana do aplikacji. |
| `client_assertion_type` | Wymagany | Wartość musi `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`być . |
| `client_assertion` | Wymagany | Twierdzenie (token internetowy JSON), które należy utworzyć i podpisać za pomocą certyfikatu zarejestrowanego jako poświadczenia dla aplikacji. Aby dowiedzieć się, jak zarejestrować certyfikat i format potwierdzenia, zobacz [poświadczenia certyfikatu](active-directory-certificate-credentials.md). |
| `assertion` | Wymagany | Wartość tokenu używanego w żądaniu. |
| `requested_token_use` | Wymagany | Określa sposób przetwarzania żądania. W przepływie OBO wartość musi `on_behalf_of`być ustawiona na . |
| `scope` | Wymagany | Oddzielona spacja lista zakresów dla żądania tokenu. Aby uzyskać więcej informacji, zobacz [zakresy](v2-permissions-and-consent.md).|

Należy zauważyć, że parametry są prawie takie same jak w `client_secret` przypadku żądania przez wspólny `client_assertion_type` klucz `client_assertion`tajny, z tą różnicą, że parametr jest zastępowany przez dwa parametry: a i .

#### <a name="example"></a>Przykład

Następujący protokół HTTP POST żąda `user.read` tokenu https://graph.microsoft.com dostępu z zakresem dla internetowego interfejsu API z certyfikatem.

```HTTP
// line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=https://graph.microsoft.com/user.read+offline_access
```

## <a name="service-to-service-access-token-response"></a>Odpowiedź tokenu dostępu usługi do usługi

Odpowiedzią na sukces jest odpowiedź JSON OAuth 2.0 z następującymi parametrami.

| Parametr | Opis |
| --- | --- |
| `token_type` | Wskazuje wartość typu tokenu. Jedynym typem, który obsługuje `Bearer`platforma tożsamości firmy Microsoft, jest . Aby uzyskać więcej informacji na temat tokenów na okaziciela, zobacz [OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| `scope` | Zakres dostępu przyznanego w tokenie. |
| `expires_in` | Czas w sekundach, że token dostępu jest prawidłowy. |
| `access_token` | Żądany token dostępu. Usługa wywołująca może używać tego tokenu do uwierzytelniania w usłudze odbierającej. |
| `refresh_token` | Token odświeżania żądanego tokenu dostępu. Usługa wywołująca może użyć tego tokenu, aby zażądać innego tokenu dostępu po wygaśnięciu bieżącego tokenu dostępu. Token odświeżania jest udostępniany tylko wtedy, gdy `offline_access` zażądano zakresu. |

### <a name="success-response-example"></a>Przykład odpowiedzi na sukces

W poniższym przykładzie pokazano odpowiedź na powodzenie https://graph.microsoft.com żądania tokenu dostępu dla internetowego interfejsu API.

```json
{
  "token_type": "Bearer",
  "scope": "https://graph.microsoft.com/user.read",
  "expires_in": 3269,
  "ext_expires_in": 0,
  "access_token": "eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkQ0NDYy0tY0hGa18wZE50MVEtc2loVzRMd2RwQVZISGpnTVdQZ0tQeVJIaGlDbUN2NkdyMEpmYmRfY1RmMUFxU21TcFJkVXVydVJqX3Nqd0JoN211eHlBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMzA1LCJuYmYiOjE0OTM5MzAzMDUsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQU9KYnFFWlRNTnEyZFcxYXpKN1RZMDlYeDdOT29EMkJEUlRWMXJ3b2ZRc1k9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJWR1ItdmtEZlBFQ2M1dWFDaENRSkFBIiwidmVyIjoiMS4wIn0.cubh1L2VtruiiwF8ut1m9uNBmnUJeYx4x0G30F7CqSpzHj1Sv5DCgNZXyUz3pEiz77G8IfOF0_U5A_02k-xzwdYvtJUYGH3bFISzdqymiEGmdfCIRKl9KMeoo2llGv0ScCniIhr2U1yxTIkIpp092xcdaDt-2_2q_ql1Ha_HtjvTV1f9XR3t7_Id9bR5BqwVX5zPO7JMYDVhUZRx08eqZcC-F3wi0xd_5ND_mavMuxe2wrpF-EZviO3yg0QVRr59tE3AoWl8lSGpVc97vvRCnp4WVRk26jJhYXFPsdk4yWqOKZqzr3IFGyD08WizD_vPSrXcCPbZP3XWaoTUKZSNJg",
  "refresh_token": "OAQABAAAAAABnfiG-mA6NTae7CdWW7QfdAALzDWjw6qSn4GUDfxWzJDZ6lk9qRw4AnqPnvFqnzS3GiikHr5wBM1bV1YyjH3nUeIhKhqJWGwqJFRqs2sE_rqUfz7__3J92JDpi6gDdCZNNaXgreQsH89kLCVNYZeN6kGuFGZrjwxp1wS2JYc97E_3reXBxkHrA09K5aR-WsSKCEjf6WI23FhZMTLhk_ZKOe_nWvcvLj13FyvSrTMZV2cmzyCZDqEHtPVLJgSoASuQlD2NXrfmtcmgWfc3uJSrWLIDSn4FEmVDA63X6EikNp9cllH3Gp7Vzapjlnws1NQ1_Ff5QrmBHp_LKEIwfzVKnLLrQXN0EzP8f6AX6fdVTaeKzm7iw6nH0vkPRpUeLc3q_aNsPzqcTOnFfgng7t2CXUsMAGH5wclAyFCAwL_Cds7KnyDLL7kzOS5AVZ3Mqk2tsPlqopAiHijZaJumdTILDudwKYCFAMpUeUwEf9JmyFjl2eIWPmlbwU7cHKWNvuRCOYVqbsTTpJthwh4PvsL5ov5CawH_TaV8omG_tV6RkziHG9urk9yp2PH9gl7Cv9ATa3Vt3PJWUS8LszjRIAJmyw_EhgHBfYCvEZ8U9PYarvgqrtweLcnlO7BfnnXYEC18z_u5wemAzNBFUje2ttpGtRmRic4AzZ708tBHva2ePJWGX6pgQbiWF8esOrvWjfrrlfOvEn1h6YiBW291M022undMdXzum6t1Y1huwxHPHjCAA"
}
```

> [!NOTE]
> Powyższy token dostępu jest tokenem w formacie v1.0. Jest to spowodowane token jest dostarczany na podstawie **zasobu,** do który uzyskuje się dostęp. Program Microsoft Graph jest skonfigurowany do akceptowania tokenów w wersji 1.0, więc platforma tożsamości firmy Microsoft tworzy tokeny dostępu w wersji 1.0, gdy klient żąda tokenów dla programu Microsoft Graph. Tylko aplikacje powinny patrzeć na tokeny dostępu. Klienci **nie mogą** ich sprawdzać.

### <a name="error-response-example"></a>Przykład odpowiedzi na błąd

Odpowiedź na błąd jest zwracana przez punkt końcowy tokenu podczas próby uzyskania tokenu dostępu dla interfejsu API podrzędnego, jeśli interfejs API podrzędnego interfejsu API ma ustawione zasady dostępu warunkowego (takie jak uwierzytelnianie wieloskładnikowe). Usługa warstwy środkowej powinna wywklić ten błąd w aplikacji klienckiej, aby aplikacja kliencka mogła zapewnić interakcję użytkownika w celu spełnienia zasad dostępu warunkowego.

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

## <a name="use-the-access-token-to-access-the-secured-resource"></a>Użyj tokenu dostępu, aby uzyskać dostęp do zabezpieczonego zasobu

Teraz usługa warstwy środkowej może używać tokenu nabyte powyżej do tworzenia uwierzytelnionych żądań `Authorization` do podrzędnego interfejsu API sieci web, ustawiając token w nagłówku.

### <a name="example"></a>Przykład

```HTTP
GET /v1.0/me HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkSzdNN0RyNXlvUUdLNmFEc19vdDF3cEQyZjNqRkxiNlVrcm9PcXA2cXBJclAxZVV0QktzMHEza29HN3RzXzJpSkYtQjY1UV8zVGgzSnktUHZsMjkxaFNBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMDE2LCJuYmYiOjE0OTM5MzAwMTYsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQUlzQjN5ZUljNkZ1aEhkd1YxckoxS1dlbzJPckZOUUQwN2FENTVjUVRtems9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJzUVlVekYxdUVVS0NQS0dRTVFVRkFBIiwidmVyIjoiMS4wIn0.Hrn__RGi-HMAzYRyCqX3kBGb6OS7z7y49XPVPpwK_7rJ6nik9E4s6PNY4XkIamJYn7tphpmsHdfM9lQ1gqeeFvFGhweIACsNBWhJ9Nx4dvQnGRkqZ17KnF_wf_QLcyOrOWpUxdSD_oPKcPS-Qr5AFkjw0t7GOKLY-Xw3QLJhzeKmYuuOkmMDJDAl0eNDbH0HiCh3g189a176BfyaR0MgK8wrXI_6MTnFSVfBePqklQeLhcr50YTBfWg3Svgl6MuK_g1hOuaO-XpjUxpdv5dZ0SvI47fAuVDdpCE48igCX5VMj4KUVytDIf6T78aIXMkYHGgW3-xAmuSyYH_Fr0yVAQ
```

## <a name="gaining-consent-for-the-middle-tier-application"></a>Uzyskanie zgody na aplikację klasy średniej

W zależności od architektury lub użycia aplikacji można rozważyć różne strategie dla zapewnienia, że przepływ OBO zakończy się pomyślnie. We wszystkich przypadkach ostatecznym celem jest zapewnienie odpowiedniej zgody jest udzielana, tak aby aplikacja kliencka może wywołać aplikację warstwy środkowej, a aplikacja warstwy środkowej ma uprawnienia do wywoływania zasobu zaplecza.

> [!NOTE]
> Wcześniej system kont Microsoft (konta osobiste) nie obsługiwał pola "Znana aplikacja kliencka", ani nie mógł wykazywać połączonej zgody.  To zostało dodane i wszystkie aplikacje na platformie tożsamości firmy Microsoft można użyć znanego podejścia aplikacji klienckiej dla gettign zgody na wywołania OBO.

### <a name="default-and-combined-consent"></a>/.default i łączna zgoda

Aplikacja warstwy środkowej dodaje klienta do listy znanych aplikacji klienckich w manifeście, a następnie klient może wyzwolić przepływ połączonej zgody zarówno dla siebie, jak i dla aplikacji warstwy środkowej. W punkcie końcowym platformy tożsamości firmy Microsoft odbywa się to przy użyciu [ `/.default` zakresu](v2-permissions-and-consent.md#the-default-scope). Podczas wyzwalania ekranu zgody przy `/.default`użyciu znanych aplikacji klienckich i na ekranie zgody będą wyświetlane uprawnienia **zarówno** dla klienta do interfejsu API warstwy środkowej, a także żądać wszelkich uprawnień wymaganych przez interfejs API warstwy środkowej. Użytkownik udostępnia zgodę dla obu aplikacji, a następnie przepływ OBO działa.

### <a name="pre-authorized-applications"></a>Wstępnie autoryzowane aplikacje

Zasoby mogą wskazywać, że dana aplikacja zawsze ma uprawnienia do odbierania niektórych zakresów. Jest to przydatne przede wszystkim do nawiązywać połączenia między klientem front-end i zasobu zaplecza bardziej bezproblemowe. Zasób może zadeklarować wiele wstępnie autoryzowanych aplikacji — każda taka aplikacja może zażądać tych uprawnień w przepływie OBO i odbierać je bez zgody użytkownika.

### <a name="admin-consent"></a>zgoda administratora

Administrator dzierżawy może zagwarantować, że aplikacje mają uprawnienia do wywoływania wymaganych interfejsów API, zapewniając zgodę administratora dla aplikacji warstwy środkowej. Aby to zrobić, administrator może znaleźć aplikację warstwy środkowej w dzierżawie, otworzyć stronę wymaganych uprawnień i wybrać, aby udzielić uprawnień do aplikacji. Aby dowiedzieć się więcej o zgodzie administratora, zobacz [dokumentację zgody i uprawnień](v2-permissions-and-consent.md).

### <a name="use-of-a-single-application"></a>Korzystanie z jednej aplikacji

W niektórych scenariuszach może mieć tylko jedno parowanie klienta warstwy środkowej i front-end. W tym scenariuszu może okazać się łatwiejsze do tej aplikacji pojedynczej, negując potrzebę aplikacji warstwy środkowej całkowicie. Aby uwierzytelnić się między interfejsem front-end a internetowym interfejsem API, można użyć plików cookie, id_token lub tokenu dostępu żądanego dla samej aplikacji. Następnie zażądaj zgody od tej pojedynczej aplikacji do zasobu zaplecza.

## <a name="client-limitations"></a>Ograniczenia klienta

Jeśli klient używa niejawnego przepływu, aby uzyskać id_token, a klient ma również symbole wieloznaczne w adresie URL odpowiedzi, id_token nie może być używany dla przepływu OBO.  Jednak tokeny dostępu nabyte za pośrednictwem niejawnego przepływu dotacji nadal mogą być zrealizowane przez poufnego klienta, nawet jeśli klient inicjujący ma zarejestrowany adres URL odpowiedzi z symbolami wieloznaczymi.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o protokole OAuth 2.0 i innym sposobem wykonywania usługi do obsługi eru przy użyciu poświadczeń klienta.

* [Udzielanie poświadczeń klienta OAuth 2.0 na platformie tożsamości firmy Microsoft](v2-oauth2-client-creds-grant-flow.md)
* [Przepływ kodu OAuth 2.0 na platformie tożsamości firmy Microsoft](v2-oauth2-auth-code-flow.md)
* [Korzystanie `/.default` z zakresu](v2-permissions-and-consent.md#the-default-scope)
