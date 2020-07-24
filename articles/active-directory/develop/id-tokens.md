---
title: Tokeny identyfikatora platformy tożsamości firmy Microsoft | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak używać id_tokens emitowanych przez punkty końcowe usługi Azure AD v 1.0 i Microsoft Identity platform (v 2.0).
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 07/21/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms:custom: fasttrack-edit
ms.openlocfilehash: 2fbbf5f9d01ed4a469967dac87faa3b130905757
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87027108"
---
# <a name="microsoft-identity-platform-id-tokens"></a>Tokeny identyfikatora platformy tożsamości firmy Microsoft

`id_tokens`są wysyłane do aplikacji klienckiej w ramach przepływu [OpenID Connect Connect](v2-protocols-oidc.md) (OIDC). Mogą być wysyłane po stronie lub zamiast tokenu dostępu i są używane przez klienta do uwierzytelniania użytkownika.

## <a name="using-the-id_token"></a>Korzystanie z id_token

Tokeny identyfikatora należy stosować do sprawdzania, czy użytkownik jest użytkownikiem, który je rości i uzyskać dodatkowych użytecznych informacji o nich — nie należy go używać do autoryzacji zamiast [tokenu dostępu](access-tokens.md). Oświadczenia, które zapewnia, może być używany dla środowiska użytkownika w aplikacji, jako klucze w bazie danych i zapewniać dostęp do aplikacji klienckiej.  Podczas tworzenia kluczy dla bazy danych `idp` nie należy używać usługi, ponieważ Messes ona scenariusze gościa.  W razie potrzeby należy wykonać `sub` tylko własne czynności (które są zawsze unikatowe) `tid` .  Jeśli musisz udostępnić dane między usługami, `oid` + `sub` + `tid` program będzie działał od czasu, gdy wiele usług uzyska te same `oid` .

## <a name="claims-in-an-id_token"></a>Oświadczenia w id_token

`id_tokens`tożsamość firmy Microsoft to [JWTs](https://tools.ietf.org/html/rfc7519) (tokeny sieci Web JSON), co oznacza, że zawierają one nagłówek, ładunek i część podpisu. Możesz użyć nagłówka i podpisu do zweryfikowania autentyczności tokenu, natomiast ładunek zawiera informacje o użytkowniku żądanym przez klienta. Wszystkie oświadczenia JWT wymienione w tym miejscu są wyświetlane zarówno w tokenach w wersji 1.0, jak i 2.0.

### <a name="v10"></a>Wersja 1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q
```

Wyświetl Ten przykładowy token v 1.0 w [JWT.MS](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q).

### <a name="v20"></a>Wersja 2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw
```

Wyświetl Ten przykładowy token v 2.0 w [JWT.MS](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw).

### <a name="header-claims"></a>Oświadczenia nagłówka

|Claim | Format | Opis |
|-----|--------|-------------|
|`typ` | Ciąg — zawsze "JWT" | Wskazuje, że token jest tokenem JWT.|
|`alg` | String (ciąg) | Wskazuje algorytm, który został użyty do podpisania tokenu. Przykład: "RS256" |
|`kid` | String (ciąg) | Odcisk palca klucza publicznego służącego do podpisywania tego tokenu. Emitowane w wersji 1.0 i 2.0 `id_tokens` . |
|`x5t` | String (ciąg) | Taka sama (w użyciu i wartość) jako `kid` . Jest to jednak starsze zgłoszenie wyemitowane tylko w wersji 1.0 w `id_tokens` celu zapewnienia zgodności. |

### <a name="payload-claims"></a>Oświadczenia ładunku

Ta lista przedstawia oświadczenia JWT, które w większości id_tokens są domyślnie (z wyjątkiem sytuacji, w których zaznaczono).  Aplikacja może jednak używać [opcjonalnych oświadczeń](active-directory-optional-claims.md) do żądania dodatkowych oświadczeń JWT w id_token.  Mogą one przedziały od `groups` roszczeń do informacji o nazwie użytkownika.

|Claim | Format | Opis |
|-----|--------|-------------|
|`aud` |  Ciąg, identyfikator URI aplikacji | Identyfikuje zamierzony odbiorcę tokenu. W programie `id_tokens` odbiorcy to identyfikator aplikacji aplikacji przypisany do aplikacji w Azure Portal. Aplikacja powinna sprawdzić poprawność tej wartości i odrzucić token, jeśli wartość nie jest zgodna. |
|`iss` |  Ciąg, identyfikator URI usługi STS | Identyfikuje usługę tokenu zabezpieczającego (STS), która konstruuje i zwraca token oraz dzierżawę usługi Azure AD, w której użytkownik został uwierzytelniony. Jeśli token został wystawiony przez punkt końcowy v 2.0, identyfikator URI zakończy się `/v2.0` .  Identyfikator GUID, który wskazuje, że użytkownik jest użytkownikiem konsumenta z konto Microsoft to `9188040d-6c67-4c5b-b112-36a304b66dad` . Twoja aplikacja powinna używać identyfikatora GUID w ramach żądania, aby ograniczyć zbiór dzierżawców, którzy mogą logować się do aplikacji, jeśli ma to zastosowanie. |
|`iat` |  int, sygnatura czasowa systemu UNIX | "Wystawiony w" wskazuje, kiedy wystąpiło uwierzytelnianie dla tego tokenu.  |
|`idp`|Ciąg, zazwyczaj identyfikator URI usługi STS | Rejestruje dostawcę tożsamości, który uwierzytelnił podmiot tokenu. Ta wartość jest taka sama jak wartość odszkodowania wystawcy, chyba że konto użytkownika nie znajduje się w tej samej dzierżawie co wystawcy, na przykład. Jeśli oświadczenia nie jest obecny, oznacza to, że `iss` można użyć zamiast niego wartości.  W przypadku kont osobistych używanych w kontekście organizacyjnym (np. konta osobistego zaproszonego do dzierżawy usługi Azure AD) `idp` może istnieć wartość "Live.com" lub identyfikator URI usługi STS zawierający dzierżawcę konto Microsoft `9188040d-6c67-4c5b-b112-36a304b66dad` . |
|`nbf` |  int, sygnatura czasowa systemu UNIX | Wartość "NBF" (nie wcześniej) określa czas, po którym nie można zatwierdzić tokenu JWT do przetwarzania.|
|`exp` |  int, sygnatura czasowa systemu UNIX | Wartość "EXP" (czas wygaśnięcia) określa czas wygaśnięcia w dniu lub, po którym nie można zaakceptować tokenu JWT do przetworzenia.  Należy pamiętać, że zasób może odrzucić token przed tym terminem, a na przykład zmiana uwierzytelniania jest wymagana lub wykryto odwołanie tokenu. |
| `c_hash`| String (ciąg) |Skrót kodu jest uwzględniany w tokenach identyfikatorów tylko wtedy, gdy token identyfikatora jest wystawiony za pomocą kodu autoryzacji OAuth 2,0. Może służyć do weryfikowania autentyczności kodu autoryzacji. Aby uzyskać szczegółowe informacje o wykonywaniu tej walidacji, zobacz [specyfikację programu OpenID Connect Connect](https://openid.net/specs/openid-connect-core-1_0.html). |
|`at_hash`| String (ciąg) |Skrót tokenu dostępu jest uwzględniany w tokenach identyfikatorów tylko wtedy, gdy token identyfikatora jest wystawiony z tokenem dostępu OAuth 2,0. Może służyć do weryfikowania autentyczności tokenu dostępu. Aby uzyskać szczegółowe informacje o wykonywaniu tej walidacji, zobacz [specyfikację programu OpenID Connect Connect](https://openid.net/specs/openid-connect-core-1_0.html). |
|`aio` | Ciąg nieprzezroczysty | Deklaracja wewnętrzna używana przez usługę Azure AD do rejestrowania danych do ponownego użycia tokenu. Powinien być ignorowany.|
|`preferred_username` | String (ciąg) | Podstawowa nazwa użytkownika, która reprezentuje użytkownika. Może to być adres e-mail, numer telefonu lub ogólna nazwa użytkownika bez określonego formatu. Jego wartość jest modyfikowalna i może ulec zmianie w czasie. Ponieważ jest modyfikowalny, ta wartość nie może być używana do podejmowania decyzji dotyczących autoryzacji. `profile`Zakres jest wymagany do otrzymania tego żądania.|
|`email` | String (ciąg) | To `email` ustawienie jest domyślnie dostępne dla kont Gości, które mają adres e-mail.  Twoja aplikacja może zażądać pożądania wiadomości e-mail dla użytkowników zarządzanych (z tej samej dzierżawy co zasób) przy użyciu `email` [opcjonalnego żądania](active-directory-optional-claims.md).  W punkcie końcowym programu v 2.0 Aplikacja może również zażądać `email` zakresu łączenia OpenID Connect — nie musisz żądać żądania opcjonalnego i zakresu w celu uzyskania żądania.  W ramach tego żądania adres e-mail jest obsługiwany tylko w przypadku wiadomości z informacjami o profilu użytkownika. |
|`name` | String (ciąg) | To zgłoszenie `name` zapewnia wartość czytelną dla człowieka, która identyfikuje podmiot tokenu. Wartość nie gwarantuje, że jest unikatowa, jest modyfikowalna i jest przeznaczona do użycia tylko na potrzeby wyświetlania. `profile`Zakres jest wymagany do otrzymania tego żądania. |
|`nonce`| String (ciąg) | Identyfikator jednorazowy jest zgodny z parametrem zawartym w oryginalnym żądaniu/Authorize do dostawcy tożsamości. Jeśli nie jest zgodny, aplikacja powinna odrzucić token. |
|`oid` | Ciąg, identyfikator GUID | Niezmienny identyfikator dla obiektu w systemie tożsamości firmy Microsoft, w tym przypadku, konto użytkownika. Ten identyfikator jednoznacznie identyfikuje użytkownika w wielu aplikacjach — dwie różne aplikacje, które logują się w tym samym użytkowniku, otrzymają taką samą wartość w ramach `oid` roszczeń. Microsoft Graph zwróci ten identyfikator jako `id` Właściwość dla danego konta użytkownika. Ponieważ `oid` umożliwia wielu aplikacjom skorelowanie użytkowników, `profile` zakres jest wymagany do otrzymania tego żądania. Należy pamiętać, że jeśli pojedynczy użytkownik istnieje w wielu dzierżawach, użytkownik będzie zawierać inny identyfikator obiektu w każdej dzierżawie — jest uznawany za różne konta, nawet jeśli użytkownik loguje się do każdego konta z tymi samymi poświadczeniami. To `oid` jest identyfikator GUID i nie można go ponownie użyć. |
|`roles`| Tablica ciągów | Zestaw ról przypisanych do użytkownika, który loguje się. |
|`rh` | Ciąg nieprzezroczysty |Wyjątek wewnętrzny używany przez platformę Azure do weryfikacji tokenów. Powinien być ignorowany. |
|`sub` | Ciąg, identyfikator GUID | Podmiot zabezpieczeń, dla którego token potwierdza informacje, takie jak użytkownik aplikacji. Ta wartość jest niezmienna i nie można jej ponownie przypisać ani ponownie użyć. Podmiot jest identyfikatorem parowania — jest unikatowy dla określonego identyfikatora aplikacji. Jeśli pojedynczy użytkownik zaloguje się do dwóch różnych aplikacji przy użyciu dwóch różnych identyfikatorów klienta, te aplikacje otrzymają dwie różne wartości dla zgłoszenia podmiotu. Może to być niepożądane, w zależności od wymagań dotyczących architektury i ochrony prywatności. |
|`tid` | Ciąg, identyfikator GUID | Identyfikator GUID reprezentujący dzierżawę usługi Azure AD, z której korzysta użytkownik. W przypadku kont służbowych identyfikator GUID jest niezmiennym IDENTYFIKATORem dzierżawy organizacji, do której należy użytkownik. W przypadku kont osobistych wartość to `9188040d-6c67-4c5b-b112-36a304b66dad` . `profile`Zakres jest wymagany do otrzymania tego żądania. |
|`unique_name` | String (ciąg) | Udostępnia zrozumiałą wartość identyfikującą podmiot tokenu. Ta wartość jest unikatowa w danym punkcie w czasie, ale jako że można ponownie użyć wiadomości e-mail i innych identyfikatorów, ta wartość może być ponownie wyświetlana na innych kontach i dlatego powinna być używana tylko do wyświetlania. Wystawione wyłącznie w wersji 1.0 `id_tokens` . |
|`uti` | Ciąg nieprzezroczysty | Wyjątek wewnętrzny używany przez platformę Azure do weryfikacji tokenów. Powinien być ignorowany. |
|`ver` | Ciąg, 1,0 lub 2,0 | Wskazuje wersję id_token. |

> [!NOTE]
> Id_token v 1.0 i v 22.0 mają różnice w ilości informacji, które będą się znajdować na podstawie powyższych przykładów. Wersja zasadniczo określa punkt końcowy platformy usługi Azure AD, z którego został wystawiony. [Implementacja protokołu OAuth usługi Azure AD](about-microsoft-identity-platform.md) została rozwijająca się w latach. Obecnie istnieją dwa różne outh punkty końcowe dla aplikacji usługi Azure AD. Można użyć dowolnego z nowych punktów końcowych, które są klasyfikowane jako wersja 2.0 lub 1.0. Punkty końcowe OAuth obu tych elementów są różne. Punkt końcowy v 2.0 jest nowszy i funkcje punktu końcowego v 1.0 są migrowane do tego punktu końcowego. Nowi deweloperzy powinni korzystać z punktu końcowego v 2.0.
>
> - v 1.0: punkty końcowe usługi Azure AD:`https://login.microsoftonline.com/common/oauth2/authorize`
> - v 2.0: punkty końcowe Microsoft identitypPlatform:`https://login.microsoftonline.com/common/oauth2/v2.0/authorize`

## <a name="validating-an-id_token"></a>Sprawdzanie poprawności id_token

Walidacja `id_token` jest podobna do pierwszego kroku [sprawdzania poprawności tokenu dostępu](access-tokens.md#validating-tokens) — klient powinien sprawdzić, czy prawidłowy wystawca przesłał token i nie został naruszony. Ponieważ `id_tokens` zawsze są tokeny JWT, wiele bibliotek istnieje do zweryfikowania tych tokenów — zalecamy użycie jednego z nich zamiast samodzielnie.

Aby ręcznie zweryfikować token, zapoznaj się ze szczegółami dotyczącymi kroków związanych z [walidacją tokenu dostępu](access-tokens.md#validating-tokens). Po sprawdzeniu podpisu w tokenie należy zweryfikować następujące oświadczenia JWT w id_token (mogą to być również w bibliotece walidacji tokenu):

* Sygnatury czasowe: `iat` , `nbf` , i `exp` sygnatury czasowe powinny przypadać przed lub po bieżącym czasie, zgodnie z potrzebami.
* Odbiorcy: wartość tego `aud` żądania powinna być zgodna z identyfikatorem aplikacji dla aplikacji.
* Identyfikator jednorazowy: żądanie `nonce` w ładunku musi być zgodne z parametrem nonce przekazaną do punktu końcowego/Authorize podczas początkowego żądania.

## <a name="next-steps"></a>Następne kroki

* Informacje o [tokenach dostępu](access-tokens.md)
* Dostosuj oświadczenia JWT w id_token przy użyciu [opcjonalnych oświadczeń](active-directory-optional-claims.md).
