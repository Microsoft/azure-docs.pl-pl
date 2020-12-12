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
ms.date: 09/09/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms:custom: fasttrack-edit
ms.openlocfilehash: 784f1cc7b7e063166dc1f24851ab217cef8d831a
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2020
ms.locfileid: "97355651"
---
# <a name="microsoft-identity-platform-id-tokens"></a>Tokeny identyfikatora platformy tożsamości firmy Microsoft

`id_tokens` są wysyłane do aplikacji klienckiej w ramach przepływu [OpenID Connect Connect](v2-protocols-oidc.md) (OIDC). Mogą być wysyłane wraz z tokenem dostępu lub zamiast niego, i są używane przez klienta do uwierzytelniania użytkownika.

## <a name="using-the-id_token"></a>Korzystanie z id_token

Tokeny identyfikatora należy stosować do sprawdzania, czy użytkownik jest użytkownikiem, który je rości i uzyskać dodatkowych użytecznych informacji o nich — nie należy go używać do autoryzacji zamiast [tokenu dostępu](access-tokens.md). Oświadczenia, które zapewnia, może być używany dla środowiska użytkownika w aplikacji, jako [klucze w bazie danych](#using-claims-to-reliably-identify-a-user-subject-and-object-id)i zapewniać dostęp do aplikacji klienckiej.  

## <a name="claims-in-an-id_token"></a>Oświadczenia w id_token

`id_tokens` są [JWTs](https://tools.ietf.org/html/rfc7519) (tokeny sieci Web JSON), co oznacza, że zawierają nagłówek, ładunek i część podpisu. Możesz użyć nagłówka i podpisu do zweryfikowania autentyczności tokenu, natomiast ładunek zawiera informacje o użytkowniku żądanym przez klienta. Wszystkie oświadczenia JWT wymienione w tym miejscu są wyświetlane zarówno w tokenach w wersji 1.0, jak i 2.0.

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
|`alg` | Ciąg | Wskazuje algorytm, który został użyty do podpisania tokenu. Przykład: "RS256" |
|`kid` | Ciąg | Odcisk palca klucza publicznego służącego do podpisywania tego tokenu. Emitowane w wersji 1.0 i 2.0 `id_tokens` . |
|`x5t` | Ciąg | Taka sama (w użyciu i wartość) jako `kid` . Jest to jednak starsze zgłoszenie wyemitowane tylko w wersji 1.0 w `id_tokens` celu zapewnienia zgodności. |

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
| `c_hash`| Ciąg |Skrót kodu jest uwzględniany w tokenach identyfikatorów tylko wtedy, gdy token identyfikatora jest wystawiony za pomocą kodu autoryzacji OAuth 2,0. Może służyć do weryfikowania autentyczności kodu autoryzacji. Aby uzyskać szczegółowe informacje o wykonywaniu tej walidacji, zobacz [specyfikację programu OpenID Connect Connect](https://openid.net/specs/openid-connect-core-1_0.html). |
|`at_hash`| Ciąg |Skrót tokenu dostępu jest uwzględniany w tokenach identyfikatorów tylko wtedy, gdy token identyfikatora jest wystawiony z `/authorize` punktu końcowego za pomocą tokenu dostępu OAuth 2,0. Może służyć do weryfikowania autentyczności tokenu dostępu. Aby uzyskać szczegółowe informacje o wykonywaniu tej walidacji, zobacz [specyfikację programu OpenID Connect Connect](https://openid.net/specs/openid-connect-core-1_0.html#HybridIDToken). Ta wartość nie jest zwracana w przypadku tokenów identyfikatora z `/token` punktu końcowego. |
|`aio` | Ciąg nieprzezroczysty | Deklaracja wewnętrzna używana przez usługę Azure AD do rejestrowania danych do ponownego użycia tokenu. Powinien być ignorowany.|
|`preferred_username` | Ciąg | Podstawowa nazwa użytkownika, która reprezentuje użytkownika. Może to być adres e-mail, numer telefonu lub ogólna nazwa użytkownika bez określonego formatu. Jego wartość jest modyfikowalna i może ulec zmianie w czasie. Ponieważ jest modyfikowalny, ta wartość nie może być używana do podejmowania decyzji dotyczących autoryzacji. `profile`Zakres jest wymagany do otrzymania tego żądania.|
|`email` | Ciąg | To `email` ustawienie jest domyślnie dostępne dla kont Gości, które mają adres e-mail.  Twoja aplikacja może zażądać pożądania wiadomości e-mail dla użytkowników zarządzanych (z tej samej dzierżawy co zasób) przy użyciu `email` [opcjonalnego żądania](active-directory-optional-claims.md).  W punkcie końcowym programu v 2.0 Aplikacja może również zażądać `email` zakresu łączenia OpenID Connect — nie musisz żądać żądania opcjonalnego i zakresu w celu uzyskania żądania.  W ramach tego żądania adres e-mail jest obsługiwany tylko w przypadku wiadomości z informacjami o profilu użytkownika. |
|`name` | Ciąg | To zgłoszenie `name` zapewnia wartość czytelną dla człowieka, która identyfikuje podmiot tokenu. Wartość nie gwarantuje, że jest unikatowa, jest modyfikowalna i jest przeznaczona do użycia tylko na potrzeby wyświetlania. `profile`Zakres jest wymagany do otrzymania tego żądania. |
|`nonce`| Ciąg | Identyfikator jednorazowy jest zgodny z parametrem zawartym w oryginalnym żądaniu/Authorize do dostawcy tożsamości. Jeśli nie jest zgodny, aplikacja powinna odrzucić token. |
|`oid` | Ciąg, identyfikator GUID | Niezmienny identyfikator dla obiektu w systemie tożsamości firmy Microsoft, w tym przypadku, konto użytkownika. Ten identyfikator jednoznacznie identyfikuje użytkownika w wielu aplikacjach — dwie różne aplikacje, które logują się w tym samym użytkowniku, otrzymają taką samą wartość w ramach `oid` roszczeń. Microsoft Graph zwróci ten identyfikator jako `id` Właściwość dla danego konta użytkownika. Ponieważ `oid` umożliwia wielu aplikacjom skorelowanie użytkowników, `profile` zakres jest wymagany do otrzymania tego żądania. Należy pamiętać, że jeśli pojedynczy użytkownik istnieje w wielu dzierżawach, użytkownik będzie zawierać inny identyfikator obiektu w każdej dzierżawie — jest uznawany za różne konta, nawet jeśli użytkownik loguje się do każdego konta z tymi samymi poświadczeniami. To `oid` jest identyfikator GUID i nie można go ponownie użyć. |
|`roles`| Tablica ciągów | Zestaw ról przypisanych do użytkownika, który loguje się. |
|`rh` | Ciąg nieprzezroczysty |Wyjątek wewnętrzny używany przez platformę Azure do weryfikacji tokenów. Powinien być ignorowany. |
|`sub` | Ciąg | Podmiot zabezpieczeń, dla którego token potwierdza informacje, takie jak użytkownik aplikacji. Ta wartość jest niezmienna i nie można jej ponownie przypisać ani ponownie użyć. Podmiot jest identyfikatorem parowania — jest unikatowy dla określonego identyfikatora aplikacji. Jeśli pojedynczy użytkownik zaloguje się do dwóch różnych aplikacji przy użyciu dwóch różnych identyfikatorów klienta, te aplikacje otrzymają dwie różne wartości dla zgłoszenia podmiotu. Może to być niepożądane, w zależności od wymagań dotyczących architektury i ochrony prywatności. |
|`tid` | Ciąg, identyfikator GUID | Identyfikator GUID reprezentujący dzierżawę usługi Azure AD, z której korzysta użytkownik. W przypadku kont służbowych identyfikator GUID jest niezmiennym IDENTYFIKATORem dzierżawy organizacji, do której należy użytkownik. W przypadku kont osobistych wartość to `9188040d-6c67-4c5b-b112-36a304b66dad` . `profile`Zakres jest wymagany do otrzymania tego żądania. |
|`unique_name` | Ciąg | Udostępnia zrozumiałą wartość identyfikującą podmiot tokenu. Ta wartość jest unikatowa w danym punkcie w czasie, ale jako że można ponownie użyć wiadomości e-mail i innych identyfikatorów, ta wartość może być ponownie wyświetlana na innych kontach i dlatego powinna być używana tylko do wyświetlania. Wystawione wyłącznie w wersji 1.0 `id_tokens` . |
|`uti` | Ciąg nieprzezroczysty | Wyjątek wewnętrzny używany przez platformę Azure do weryfikacji tokenów. Powinien być ignorowany. |
|`ver` | Ciąg, 1,0 lub 2,0 | Wskazuje wersję id_token. |
|`hasgroups`|Wartość logiczna|Jeśli jest obecny, zawsze prawda, oznacza to, że użytkownik należy do co najmniej jednej grupy. Używane zamiast roszczeń grupowych dla JWTs w niejawnym wyznaczonym przepływie, jeśli w ramach żądania Full Groups zostanie rozbudowany fragment identyfikatora URI wykraczający poza limity długości adresów URL (obecnie 6 lub więcej grup). Wskazuje, że klient powinien używać interfejsu API Microsoft Graph do określenia grup użytkownika ( `https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects` ).|
|`groups:src1`|Obiekt JSON | W przypadku żądań tokenów, które nie mają ograniczonej długości (patrz `hasgroups` powyżej), ale wciąż za duże dla tokenu, zostanie uwzględniony link do listy pełnych grup dla użytkownika. W przypadku JWTs jako roszczeń rozproszonych, w przypadku protokołu SAML jako nowego odszkodowania zamiast `groups` zgłoszenia. <br><br>**Przykładowa wartość JWT**: <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects" }`<br><br> Aby uzyskać więcej informacji, zobacz [Group nadwyżkowe żądania](#groups-overage-claim).|

> [!NOTE]
> W wersji 1.0 i 2.0 id_token istnieją różnice w ilości informacji, które będą się znajdować na podstawie powyższych przykładów. Wersja jest oparta na punkcie końcowym, w którym został żądany. Istniejące aplikacje mogą korzystać z punktu końcowego usługi Azure AD, dlatego nowe aplikacje powinny korzystać z punktu końcowego "Microsoft Identity platform" w wersji 2.0.
>
> - v 1.0: punkty końcowe usługi Azure AD: `https://login.microsoftonline.com/common/oauth2/authorize`
> - v 2.0: punkty końcowe platformy tożsamości firmy Microsoft: `https://login.microsoftonline.com/common/oauth2/v2.0/authorize`

### <a name="using-claims-to-reliably-identify-a-user-subject-and-object-id"></a>Używanie oświadczeń do niezawodnego identyfikowania użytkownika (podmiot i identyfikator obiektu)

W przypadku identyfikowania użytkownika (np. wyszukiwania ich w bazie danych lub decydowania o posiadanych uprawnieniach) krytyczne jest użycie informacji, które pozostaną stałe i unikatowe w czasie. Starsze aplikacje czasami używają pól, takich jak adres e-mail, numer telefonu lub nazwa UPN.  Wszystkie te dane mogą ulec zmianie z upływem czasu, a także mogą być ponownie używane w czasie, gdy pracownik zmieni swoją nazwę lub pracownik otrzyma adres e-mail pasujący do poprzedniego, nieobecnego pracownika. W związku z tym **Ważne** jest, aby aplikacja nie korzystała z danych przez człowieka do identyfikowania użytkownika, który jest czytelny, co oznacza, że ktoś odczyta go i chce go zmienić. Zamiast tego należy użyć oświadczeń dostarczonych przez Standard OIDC lub oświadczenia rozszerzeń dostarczonych przez firmę Microsoft — `sub` `oid` oświadczenia i.

Poprawne przechowywanie informacji dla poszczególnych użytkowników, użycie `sub` lub `oid` samodzielne (które identyfikatory GUID są unikatowe), w `tid` razie potrzeby, używane do routingu lub fragmentowania.  Jeśli musisz udostępnić dane między usługami, najlepiej, gdy `oid` + `tid` wszystkie aplikacje uzyskają takie same `oid` i `tid` oświadczenia dla danego użytkownika.  Na `sub` platformie tożsamości firmy Microsoft jest to "para", która jest unikatowa w oparciu o kombinację adresatów tokenów, dzierżawców i użytkowników.  W ten sposób dwie aplikacje, które żądają tokenów identyfikatora dla danego użytkownika, będą otrzymywać różne `sub` oświadczenia, ale te same `oid` oświadczenia dla danego użytkownika.

>[!NOTE]
> Nie należy używać `idp` roszczeń do przechowywania informacji o użytkowniku w próbie skorelowania użytkowników w dzierżawach.  Nie będzie ona działać, ponieważ `oid` i `sub` oświadczenia dla użytkownika zmienią się między dzierżawcami, w celu zapewnienia, że aplikacje nie będą mogli śledzić użytkowników w dzierżawach.  
>
> Scenariusze gościa, w przypadku których użytkownik znajduje się w jednej dzierżawie i uwierzytelnia w innym, powinny traktować użytkownika tak, jakby był to zupełnie nowy użytkownik usługi.  Twoje dokumenty i uprawnienia w dzierżawie contoso nie powinny być stosowane w dzierżawie firmy Fabrikam. Jest to ważne, aby zapobiec przypadkowemu wyciekowi danych między dzierżawcami.

### <a name="groups-overage-claim"></a>Zgłoszenie nadwyżkowe grup
Aby mieć pewność, że rozmiar tokenu nie przekracza limitów rozmiaru nagłówka HTTP, usługa Azure AD ogranicza liczbę identyfikatorów obiektów uwzględnionych w ramach `groups` żądania. Jeśli użytkownik jest członkiem większej liczby grup niż limit nadwyżkowy (150 dla tokenów SAML, 200 dla tokenów JWT), usługa Azure AD nie emituje roszczeń grupowych w tokenie. Zamiast tego zawiera w tokenie wystąpienie nadwyżkowe, które wskazuje aplikacji, w której ma być wysyłana kwerenda Microsoft Graph interfejsu API w celu pobrania członkostwa w grupie użytkownika.

```json
{
  ...
  "_claim_names": {
   "groups": "src1"
    },
    {
  "_claim_sources": {
    "src1": {
        "endpoint":"[Url to get this user's group membership from]"
        }
       }
     }
  ...
 }
```

## <a name="validating-an-id_token"></a>Sprawdzanie poprawności id_token

Walidacja `id_token` jest podobna do pierwszego kroku [sprawdzania poprawności tokenu dostępu](access-tokens.md#validating-tokens) — klient może sprawdzić, czy prawidłowy wystawca przesłał token i nie został naruszony. Ponieważ `id_tokens` zawsze są tokeny JWT, wiele bibliotek istnieje do zweryfikowania tych tokenów — zalecamy użycie jednego z nich zamiast samodzielnie.  Należy zauważyć, że tylko poufne klienci (z wpisem tajnym) powinni weryfikować tokeny identyfikatorów.  Aplikacje publiczne (kod działający całkowicie na urządzeniu lub sieci, które nie są kontrolowane — na przykład przeglądarka użytkownika lub ich sieć domowa) nie korzystają z weryfikacji tokenu identyfikatora, ponieważ złośliwy użytkownik może przechwycić i edytować klucze używane do weryfikacji tokenu.

Aby ręcznie zweryfikować token, zapoznaj się ze szczegółami dotyczącymi kroków związanych z [walidacją tokenu dostępu](access-tokens.md#validating-tokens). Po sprawdzeniu podpisu w tokenie należy zweryfikować następujące oświadczenia JWT w id_token (mogą to być również w bibliotece walidacji tokenu):

* Sygnatury czasowe: `iat` , `nbf` , i `exp` sygnatury czasowe powinny przypadać przed lub po bieżącym czasie, zgodnie z potrzebami.
* Odbiorcy: wartość tego `aud` żądania powinna być zgodna z identyfikatorem aplikacji dla aplikacji.
* Identyfikator jednorazowy: żądanie `nonce` w ładunku musi być zgodne z parametrem nonce przekazaną do punktu końcowego/Authorize podczas początkowego żądania.

## <a name="next-steps"></a>Następne kroki

* Informacje o [tokenach dostępu](access-tokens.md)
* Dostosuj oświadczenia JWT w id_token przy użyciu [opcjonalnych oświadczeń](active-directory-optional-claims.md).
