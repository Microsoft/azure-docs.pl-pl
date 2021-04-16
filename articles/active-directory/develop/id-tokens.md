---
title: Tokeny identyfikatorów platform tożsamości firmy Microsoft | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak używać id_tokens emitowanych przez punkty końcowe usługi Azure AD w wersji 1.0 i platformy tożsamości firmy Microsoft (wersja 2.0).
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/02/2021
ms.author: hirsin
ms.reviewer: hirsin
ms.custom:
- aaddev
- identityplatformtop40
- fasttrack-edit
ms.openlocfilehash: 885379a02c8866f2829fb681683a93b1d8d314fa
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530028"
---
# <a name="microsoft-identity-platform-id-tokens"></a>Tokeny identyfikatora platformy tożsamości firmy Microsoft

`id_tokens` Są wysyłane do aplikacji klienckiej w ramach [przepływu OpenID Connect](v2-protocols-oidc.md) (OIDC). Mogą być wysyłane razem lub zamiast tokenu dostępu i są używane przez klienta do uwierzytelniania użytkownika.

## <a name="using-the-id_token"></a>Korzystanie z id_token

Tokeny identyfikatorów powinny być używane do weryfikowania, czy użytkownik jest tym, za kogo się podają, i uzyskania dodatkowych przydatnych informacji o nich — nie powinny być używane do autoryzacji w miejsce [tokenu dostępu.](access-tokens.md) Zapewniane oświadczenia mogą być używane w środowiskach użytkownika wewnątrz aplikacji jako klucze w bazie danych [i](#using-claims-to-reliably-identify-a-user-subject-and-object-id)zapewniają dostęp do aplikacji klienckiej.  

## <a name="claims-in-an-id_token"></a>Oświadczenia w id_token

`id_tokens` są [tokenami sieci](https://tools.ietf.org/html/rfc7519) Web JWT( JSON Web Tokens), co oznacza, że składają się z części nagłówka, ładunku i podpisu. Możesz użyć nagłówka i podpisu, aby zweryfikować autentyczność tokenu, podczas gdy ładunek zawiera informacje o użytkowniku żądanym przez klienta. Z wyjątkiem sytuacji, gdy wspomniano, wszystkie oświadczenia JWT wymienione w tym miejscu są wyświetlane w tokenach w wersji 1.0 i 2.0.

### <a name="v10"></a>Wersja 1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q
```

Wyświetl ten przykładowy token w wersji 1.0 w [jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q).

### <a name="v20"></a>Wersja 2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw
```

Wyświetl ten przykładowy token w wersji 2.0 w [jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw).

### <a name="header-claims"></a>Oświadczenia nagłówka

|Claim | Format | Opis |
|-----|--------|-------------|
|`typ` | Ciąg — zawsze "JWT" | Wskazuje, że token jest tokenem JWT.|
|`alg` | Ciąg | Wskazuje algorytm, który został użyty do podpisania tokenu. Przykład: "RS256" |
|`kid` | Ciąg | Odcisk palca klucza publicznego używanego do weryfikowania tego tokenu. Emitowane zarówno w wersji 1.0, jak i 2.0. `id_tokens` |
|`x5t` | Ciąg | To samo (w użyciu i wartości) co `kid` . Jednak jest to starsze oświadczenie emitowane tylko w wersji 1.0 `id_tokens` w celu zapewnienia zgodności. |

### <a name="payload-claims"></a>Oświadczenia ładunku

Ta lista zawiera oświadczenia JWT, które znajdują się w większości id_tokens domyślnie (z wyjątkiem sytuacji, gdy jest to zaznaczone).  Jednak aplikacja może używać opcjonalnych [oświadczeń do](active-directory-optional-claims.md) żądania dodatkowych oświadczeń JWT w id_token.  Może to być zakres od `groups` oświadczenia po informacje o nazwie użytkownika.

|Claim | Format | Opis |
|-----|--------|-------------|
|`aud` |  Ciąg, identyfikator URI identyfikatora aplikacji | Identyfikuje zamierzonego adresata tokenu. W programie odbiorcy to identyfikator aplikacji przypisany do aplikacji w `id_tokens` Azure Portal. Aplikacja powinna zweryfikować tę wartość i odrzucić token, jeśli wartość nie jest dopasowana. |
|`iss` |  Ciąg, URI stS | Identyfikuje usługę tokenu zabezpieczającego (STS), która tworzy i zwraca token, oraz dzierżawę usługi Azure AD, w której uwierzytelniono użytkownika. Jeśli token został wystawiony przez punkt końcowy w wersji 2.0, jego parametr URI zakończy się na `/v2.0` .  Identyfikator GUID, który wskazuje, że użytkownik jest użytkownikiem konsumentem z konto Microsoft to `9188040d-6c67-4c5b-b112-36a304b66dad` . Aplikacja powinna używać części identyfikatora GUID oświadczenia, aby ograniczyć zestaw dzierżaw, które mogą logować się do aplikacji, jeśli ma to zastosowanie. |
|`iat` |  int, sygnatura czasowa systemu UNIX | "Wystawiony o" wskazuje, kiedy wystąpiło uwierzytelnianie dla tego tokenu.  |
|`idp`|Ciąg, zazwyczaj sts URI | Rejestruje dostawcę tożsamości, który uwierzytelnił podmiot tokenu. Ta wartość jest taka sama jak wartość oświadczenia wystawcy, chyba że konto użytkownika nie należy do tej samej dzierżawy co wystawca — na przykład goście. Jeśli oświadczenie nie jest obecne, oznacza to, że zamiast tego `iss` można użyć wartości .  W przypadku kont osobistych używanych w kontekście organizacyjnym (na przykład konta osobistego zaproszonego do dzierżawy usługi Azure AD) oświadczeniem może być "live.com" lub URI usługi STS zawierający dzierżawę usługi `idp` konto Microsoft `9188040d-6c67-4c5b-b112-36a304b66dad` . |
|`nbf` |  int, sygnatura czasowa systemu UNIX | Oświadczenie "nbf" (nie przed) identyfikuje czas, przed którym JWT NIE MOŻE zostać zaakceptowany do przetwarzania.|
|`exp` |  int, sygnatura czasowa systemu UNIX | Oświadczenie "exp" (czas wygaśnięcia) identyfikuje czas wygaśnięcia dla lub po upływie którego JWT NIE MOŻE zostać zaakceptowany do przetwarzania.  Należy pamiętać, że zasób może również odrzucać token przed tym czasem — jeśli na przykład wymagana jest zmiana uwierzytelniania lub wykryto odwołanie tokenu. |
| `c_hash`| Ciąg |Skrót kodu jest uwzględniany w tokenach identyfikatorów tylko wtedy, gdy token identyfikatora jest wystawiony przy użyciu kodu autoryzacji OAuth 2.0. Może służyć do weryfikowania autentyczności kodu autoryzacji. Aby uzyskać szczegółowe informacje na temat przeprowadzania tej walidacji, zobacz [OpenID Connect specyfikacji .](https://openid.net/specs/openid-connect-core-1_0.html) |
|`at_hash`| Ciąg |Skrót tokenu dostępu jest uwzględniany w tokenach identyfikatorów tylko wtedy, gdy token identyfikatora jest wystawiony z punktu końcowego przy użyciu tokenu dostępu `/authorize` OAuth 2.0. Może służyć do weryfikowania autentyczności tokenu dostępu. Aby uzyskać szczegółowe informacje na temat przeprowadzania tej walidacji, zobacz [OpenID Connect specyfikacji .](https://openid.net/specs/openid-connect-core-1_0.html#HybridIDToken) Nie jest to zwracane w przypadku tokenów identyfikatorów z punktu `/token` końcowego. |
|`aio` | Nieprzezroczysty ciąg | Oświadczenie wewnętrzne używane przez usługę Azure AD do rejestracji danych do ponownego użycia tokenu. Należy zignorować.|
|`preferred_username` | Ciąg | Podstawowa nazwa użytkownika reprezentująca użytkownika. Może to być adres e-mail, numer telefonu lub ogólna nazwa użytkownika bez określonego formatu. Jej wartość jest modyfikowalna i może zmieniać się z czasem. Ponieważ jest modyfikowalna, ta wartość nie może być używana do podejmowania decyzji dotyczących autoryzacji. Zakres `profile` jest wymagany do otrzymania tego oświadczenia.|
|`email` | Ciąg | Oświadczenie `email` jest domyślnie obecne w przypadku kont gości, które mają adres e-mail.  Aplikacja może zażądać oświadczenia e-mail dla użytkowników zarządzanych (tych z tej samej dzierżawy co zasób) przy użyciu `email` [opcjonalnego oświadczenia](active-directory-optional-claims.md).  W punkcie końcowym w wersji 2.0 aplikacja może również zażądać zakresu usługi OpenID Connect — nie musisz żądać zarówno opcjonalnego oświadczenia, jak i zakresu, aby uzyskać `email` oświadczenie.  Oświadczenie e-mail obsługuje tylko adresowalna wiadomość e-mail z informacji o profilu użytkownika. |
|`name` | Ciąg | Oświadczenie `name` zawiera wartość czytelną dla człowieka, która identyfikuje podmiot tokenu. Nie ma gwarancji, że wartość będzie unikatowa, modyfikowalna i będzie używana tylko do celów wyświetlania. Zakres `profile` jest wymagany do otrzymania tego oświadczenia. |
|`nonce`| Ciąg | Wartość nonce pasuje do parametru zawartego w oryginalnym żądaniu /authorize do dostawcy tożsamości. Jeśli nie jest ona dopasowana, aplikacja powinna odrzucić token. |
|`oid` | Ciąg, identyfikator GUID | Niezmienny identyfikator obiektu w systemie tożsamości firmy Microsoft, w tym przypadku konta użytkownika. Ten identyfikator jednoznacznie identyfikuje użytkownika w aplikacjach — dwie różne aplikacje logując się do tego samego użytkownika otrzymają tę samą wartość w `oid` oświadczenie. Identyfikator Microsoft Graph zwraca ten identyfikator jako właściwość `id` dla danego konta użytkownika. Ponieważ umożliwia `oid` wielu aplikacjom korelowanie użytkowników, zakres jest wymagany do `profile` otrzymania tego oświadczenia. Należy pamiętać, że jeśli jeden użytkownik istnieje w wielu dzierżawach, będzie on zawierał inny identyfikator obiektu w każdej dzierżawie — będzie uznawany za różne konta, nawet jeśli użytkownik loguje się do każdego konta przy użyciu tych samych poświadczeń. Oświadczenie `oid` jest identyfikatorem GUID i nie można go użyć ponownie. |
|`roles`| Tablica ciągów | Zestaw ról przypisanych do użytkownika, który się loguje. |
|`rh` | Nieprzezroczysty ciąg |Oświadczenie wewnętrzne używane przez platformę Azure do ponownego uwierzytelniania tokenów. Należy zignorować. |
|`sub` | Ciąg | Podmiot zabezpieczeń, o którym token potwierdza informacje, takie jak użytkownik aplikacji. Ta wartość jest niezmienna i nie można jej ponownie przypisać ani używać ponownie. Temat jest identyfikatorem parowania — jest unikatowy dla określonego identyfikatora aplikacji. Jeśli jeden użytkownik korzysta z dwóch różnych aplikacji przy użyciu dwóch różnych identyfikatorów klientów, te aplikacje otrzymają dwie różne wartości dla oświadczenia podmiotu. Może to być zależne od architektury i wymagań dotyczących prywatności. |
|`tid` | Ciąg, identyfikator GUID | Identyfikator GUID reprezentujący dzierżawę usługi Azure AD, z których pochodzi użytkownik. W przypadku kont służbowych identyfikator GUID jest niezmiennym identyfikatorem dzierżawy organizacji, do której należy użytkownik. W przypadku kont osobistych wartość to `9188040d-6c67-4c5b-b112-36a304b66dad` . Zakres `profile` jest wymagany do otrzymania tego oświadczenia. |
|`unique_name` | Ciąg | Udostępnia zrozumiałą wartość identyfikującą podmiot tokenu. Ta wartość jest unikatowa w dowolnym momencie, ale ponieważ wiadomości e-mail i inne identyfikatory mogą być ponownie używane, ta wartość może ponownie pojawić się na innych kontach i powinna być używana tylko do celów wyświetlania. Wystawiony tylko w wersji 1.0. `id_tokens` |
|`uti` | Nieprzezroczysty ciąg | Oświadczenie wewnętrzne używane przez platformę Azure do ponownego uwierzytelniania tokenów. Należy zignorować. |
|`ver` | Ciąg 1.0 lub 2.0 | Wskazuje wersję id_token. |
|`hasgroups`|Wartość logiczna|Jeśli istnieje, zawsze ma wartość true, oznaczając, że użytkownik należy do co najmniej jednej grupy. Używane w miejsce oświadczenia grup dla JWT w niejawnych przepływach udzielania, jeśli oświadczenie pełnego grupy rozszerzyłoby fragment URI poza limity długości adresu URL (obecnie 6 lub więcej grup). Wskazuje, że klient powinien używać interfejsu API Microsoft Graph do określania grup użytkownika ( `https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects` ).|
|`groups:src1`|Obiekt JSON | W przypadku żądań tokenów, które nie mają ograniczonej długości (zobacz powyżej), ale nadal są zbyt duże dla tokenu, zostanie dołączony link do pełnej listy `hasgroups` grup dla użytkownika. W przypadku JWT jako oświadczenia rozproszonego, dla SAML jako nowe oświadczenie w miejsce `groups` oświadczenia. <br><br>**Przykładowa wartość JWT:** <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects" }`<br><br> Aby uzyskać więcej informacji, zobacz [Grupy overage claim](#groups-overage-claim).|

> [!NOTE]
> W wersjach 1.0 i 2.0 id_token różnice w ilości informacji, które będą one przenosić, jak powspomniano w powyższych przykładach. Wersja jest oparta na punkcie końcowym, z którego została zażądana. Chociaż istniejące aplikacje prawdopodobnie używają punktu końcowego usługi Azure AD, nowe aplikacje powinny używać "platformy tożsamości firmy Microsoft".
>
> - Wersja 1.0: punkty końcowe usługi Azure AD: `https://login.microsoftonline.com/common/oauth2/authorize`
> - Wersja 2.0: Punkty końcowe platformy tożsamości firmy Microsoft: `https://login.microsoftonline.com/common/oauth2/v2.0/authorize`

### <a name="using-claims-to-reliably-identify-a-user-subject-and-object-id"></a>Używanie oświadczeń do niezawodnego identyfikowania użytkownika (identyfikator podmiotu i obiektu)

Podczas identyfikowania użytkownika (np. podczas szukania go w bazie danych lub podejmowania decyzji o tym, jakie uprawnienia ma), bardzo ważne jest, aby używać informacji, które pozostaną stałe i unikatowe w czasie. Starsze aplikacje czasami używają pól, takich jak adres e-mail, numer telefonu lub upn.  Wszystkie te dane mogą zmieniać się w czasie, a także mogą być ponownie używane w czasie — gdy pracownik zmieni swoje imię i nazwisko lub pracownikowi zostanie podany adres e-mail, który pasuje do poprzedniego, już nie obecnego pracownika). W związku  z tym bardzo ważne jest, aby aplikacja nie używać danych czytelnych dla człowieka do identyfikowania użytkownika — czytelny dla człowieka oznacza, że ktoś je odczyta i chce go zmienić. Zamiast tego należy użyć oświadczeń dostarczonych przez standard OIDC lub oświadczeń rozszerzeń dostarczonych przez firmę Microsoft — `sub` oświadczeń i `oid` .

Aby prawidłowo przechowywać informacje na użytkownika, należy użyć lub samodzielnie (które jako identyfikatory GUID są unikatowe) z używanym do routingu lub fragmentowania w `sub` `oid` razie `tid` potrzeby.  Jeśli musisz udostępniać dane między usługami, najlepiej, ponieważ wszystkie aplikacje uzyskają te same dane i `oid` + `tid` oświadczenia dla `oid` danego `tid` użytkownika.  Oświadczenie na platformie tożsamości firmy Microsoft jest "oparte na parach" — jest unikatowe w oparciu o kombinację `sub` odbiorcy tokenu, dzierżawy i użytkownika.  W związku z tym dwie aplikacje, które żądają tokenów identyfikatorów dla danego użytkownika, otrzymają różne oświadczenia, ale te same oświadczenia `sub` `oid` dla tego użytkownika.

>[!NOTE]
> Nie należy używać oświadczenia do przechowywania informacji o użytkowniku podczas próby `idp` skorelowania użytkowników między dzierżawami.  Nie będzie ona działać zgodnie z oświadczeniami i dla użytkownika, które zmieniają się w dzierżawach, aby zagwarantować, że aplikacje nie będą w stanie śledzić użytkowników `oid` `sub` w różnych dzierżawach.  
>
> Scenariusze gościa, w których użytkownik znajduje się w jednej dzierżawie i uwierzytelnia się w innej, powinny traktować go tak, jakby był zupełnie nowym użytkownikiem usługi.  Dokumenty i uprawnienia w dzierżawie firmy Contoso nie powinny być stosowane w dzierżawie firmy Fabrikam. Jest to ważne, aby zapobiec przypadkowemu wyciekowi danych między dzierżawami.

### <a name="groups-overage-claim"></a>Grupuje oświadczenie o przesłonie
Aby upewnić się, że rozmiar tokenu nie przekracza limitów rozmiaru nagłówka HTTP, usługa Azure AD ogranicza liczbę identyfikatorów obiektów, które zawiera w `groups` oświadczenie. Jeśli użytkownik jest członkiem większej liczby grup niż limit nadanych zasobów (150 dla tokenów SAML, 200 tokenów JWT), usługa Azure AD nie emituje uwierzytelniania grup w tokenie. Zamiast tego zawiera w tokenie oświadczenie dotyczące naddatku, które wskazuje aplikacji na odpytowanie interfejsu API Microsoft Graph w celu pobrania członkostwa użytkownika w grupie.

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

## <a name="id-token-lifetime"></a>Okres istnienia tokenu identyfikatora

Domyślnie token identyfikatora jest ważny przez 1 godzinę — po 1 godzinie klient musi uzyskać nowy token identyfikatora.

Okres istnienia tokenu identyfikatora można dostosować, aby kontrolować, jak często aplikacja kliencza wygasa w sesji aplikacji oraz jak często użytkownik musi ponownie uwierzytelniać się (w trybie dyskretnym lub interaktywnym). Aby uzyskać więcej informacji, zobacz [Konfigurowanie okresów istnienia tokenów.](active-directory-configurable-token-lifetimes.md)

## <a name="validating-an-id_token"></a>Sprawdzania poprawności id_token

Walidacja tokenu jest podobna do pierwszego kroku weryfikacji tokenu dostępu — klient może sprawdzić, czy prawidłowy wystawca wysłał z powrotem token i czy nie został `id_token` naruszony. [](access-tokens.md#validating-tokens) Ponieważ token JWT jest zawsze tokenem JWT, istnieje wiele bibliotek do weryfikowania tych tokenów — zalecamy użycie jednego z nich zamiast `id_tokens` samodzielnie.  Należy pamiętać, że tylko klienci poufni (ci z kluczem tajnym) powinni weryfikować tokeny identyfikatorów.  Aplikacje publiczne (kod uruchomiony całkowicie na urządzeniu lub w sieci, których nie kontrolujesz — na przykład przeglądarka użytkownika lub jego sieć domowa) nie korzystają z walidacji tokenu identyfikatora, ponieważ złośliwy użytkownik może przechwycić i edytować klucze używane do walidacji tokenu.

Aby ręcznie zweryfikować token, zobacz szczegóły kroków [weryfikacji tokenu dostępu.](access-tokens.md#validating-tokens) Po weryfikacji podpisu tokenu następujące oświadczenia JWT powinny zostać zweryfikowane w id_token (mogą być również wykonywane przez bibliotekę walidacji tokenu):

* Znaczniki czasu: wszystkie znaczniki czasu , i powinny odpowiednio wypadać przed bieżącą godziną lub `iat` `nbf` po `exp` bieżącej godzinie.
* Odbiorcy: `aud` oświadczenie powinno odpowiadać identyfikatorowi aplikacji.
* Nonce: oświadczenie w ładunku musi być zgodne z parametrem nonce przekazanym do punktu końcowego `nonce` /authorize podczas początkowego żądania.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej [o tokenach dostępu](access-tokens.md)
* Dostosuj oświadczenia JWT w swoim id_token użyciu [opcjonalnych oświadczeń](active-directory-optional-claims.md).
