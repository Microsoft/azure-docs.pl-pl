---
title: Przykłady komunikacji z usługami wystawców (wersja zapoznawcza) — Azure Active Directory poświadczenia weryfikowalne
description: Szczegóły komunikacji między dostawcą tożsamości i usługą wystawcy
author: barclayn
manager: davba
ms.service: identity
ms.subservice: verifiable-credentials
ms.workload: identity
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 6aa502e1ed0e49192220174d5a8573690035a4a3
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739138"
---
# <a name="issuer-service-communication-examples-preview"></a>Przykłady komunikacji z usługami wystawców (wersja zapoznawcza)

Usługa Azure AD Weryfikowalne poświadczenia może wystawiać weryfikowalne poświadczenia, pobierania oświadczeń z tokenu identyfikatora wygenerowanego przez dostawcę tożsamości zgodnego z identyfikatorem OpenID organizacji. W tym artykule poinstruujemy Cię, jak skonfigurować dostawcę tożsamości, aby wystawca tożsamości był w stanie komunikować się z nim i pobierać prawidłowy token identyfikatora do przekazania do usługi wystawiającego. 

> [!IMPORTANT]
> Azure Active Directory poświadczenia weryfikowalne są obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Aby wydać poświadczenia weryfikowalne, wystawca uwierzytelnienia jest instruowany przez pobranie kontraktu w celu zebrania danych wejściowych od użytkownika i wysłania tych informacji do usługi wystawiającego. Jeśli musisz użyć tokenu identyfikatora, musisz skonfigurować dostawcę tożsamości, aby umożliwić aplikacji Authenticator logowanie użytkownika przy użyciu protokołu OpenID Connect tożsamości. Oświadczenia w wynikowy token identyfikatora są używane do wypełniania zawartości weryfikowalnych poświadczeń. Wystawca uwierzytelnienia uwierzytelnia użytkownika przy użyciu przepływu OpenID Connect autoryzacji. Dostawca OpenID musi obsługiwać następujące OpenID Connect funkcji: 

| Cecha | Opis |
| ------- | ----------- |
| Typ udzielenia | Musi obsługiwać typ udzielenia kodu autoryzacji. |
| Format tokenu | Musi tworzyć niezaszyfrowane compact JWTs. |
| Algorytm podpisu | Musi tworzyć WTS podpisane przy użyciu RS 256. |
| Dokument konfiguracji | Musi obsługiwać OpenID Connect konfiguracji systemu i `jwks_uri` . | 
| Rejestracja klienta | Musi obsługiwać publiczną rejestrację klienta przy `redirect_uri` użyciu wartości `vcclient://openid/` . | 
| PKCE | Zalecane ze względów bezpieczeństwa, ale nie wymagane. |

Poniżej przedstawiono przykłady żądań HTTP wysyłanych do dostawcy tożsamości. Dostawca tożsamości musi zaakceptować te żądania i odpowiedzieć na nie zgodnie z OpenID Connect standardem uwierzytelniania.

## <a name="client-registration"></a>Rejestracja klienta

Aby uzyskać weryfikowalne poświadczenia, użytkownicy muszą zalogować się do Twojego dostawcy tożsamości z Microsoft Authenticator aplikacji. 

Aby włączyć tę wymianę, zarejestruj aplikację u dostawcy tożsamości. Jeśli używasz usługi Azure AD, instrukcje można znaleźć [tutaj.](../develop/quickstart-register-app.md) Podczas rejestrowania użyj następujących wartości.

| Ustawienie | Wartość |
| ------- | ----- |
| Nazwa aplikacji | `<Issuer Name> Verifiable Credential Service` |
| Identyfikator URI przekierowania | `vcclient://openid/ ` |


Po zarejestrowaniu aplikacji u dostawcy tożsamości zarejestruj jej identyfikator klienta. Użyjemy go w sekcji poniżej. Należy również zapisać adres URL dobrze znanego punktu końcowego dla dostawcy tożsamości zgodnego z OIDC. Usługa wystawiania korzysta z tego punktu końcowego do pobrania kluczy publicznych potrzebnych do zweryfikowania tokenu identyfikatora po wysłaniu go przez aplikację Authenticator.

Skonfigurowany identyfikator URI przekierowania jest używany przez aplikację Authenticator, aby wie, kiedy logowanie zostanie zakończone, i może pobrać token identyfikatora. 

## <a name="authorization-request"></a>Żądanie autoryzacji

Żądanie autoryzacji wysyłane do dostawcy tożsamości używa następującego formatu.

```HTTP
GET /authorize?client_id=<client-id>&redirect_uri=portableidentity%3A%2F%2Fverify&response_mode=query&response_type=code&scope=openid&state=12345&nonce=12345 HTTP/1.1
Host: www.contoso.com
Connection: Keep-Alive
```

| Parametr | Wartość |
| ------- | ----------- |
| `client_id` | Identyfikator klienta uzyskany podczas procesu rejestracji aplikacji. |
| `redirect_uri` | Musi `vcclient://openid/` używać . |
| `response_mode` | Musi `query` obsługiwać . |
| `response_type` | Musi obsługiwać `code` . |
| `scope` | Musi obsługiwać `openid` . |
| `state` | Muszą być zwracane do klienta zgodnie z OpenID Connect standardem. |
| `nonce` | Musi być zwracana jako oświadczenie w tokenie identyfikatora zgodnie z OpenID Connect standardem. |

Po otrzymaniu żądania autoryzacji dostawca tożsamości powinien uwierzytelnić użytkownika i podjąć wszelkie kroki niezbędne do ukończenia logowania, takie jak uwierzytelnianie wieloskładnikowe.

Proces logowania można dostosować do swoich potrzeb. Możesz poprosić użytkowników o podanie dodatkowych informacji, zaakceptowanie warunków użytkowania usługi, zapłacenia za poświadczenia i nie tylko. Po zakończeniu wszystkich kroków odpowiedz na żądanie autoryzacji, przekierując do przekierowania URI, jak pokazano poniżej. 

```HTTP
vcclient://openid/?code=nbafhjbh1ub1yhbj1h4jr1&state=12345
```

| Parametr | Wartość |
| ------- | ----------- |
| `code` |  Kod autoryzacji zwrócony przez dostawcę tożsamości. |
| `state` | Muszą być zwracane do klienta zgodnie z OpenID Connect standardem. |

## <a name="token-request"></a>Żądanie tokenu

Żądanie tokenu wysyłane do dostawcy tożsamości będzie mieć następującą postać.

```HTTP
POST /token HTTP/1.1
Host: www.contoso.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 291

client_id=<client-id>&redirect_uri=vcclient%3A%2F%2Fopenid%2F&grant_type=authorization_code&code=nbafhjbh1ub1yhbj1h4jr1&scope=openid
```

| Parametr | Wartość |
| ------- | ----------- |
| `client_id` | Identyfikator klienta uzyskany podczas procesu rejestracji aplikacji. |
| `redirect_uri` | Musi `vcclient://openid/` używać . |
| `scope` | Musi obsługiwać `openid` . |
| `grant_type` | Musi obsługiwać `authorization_code` . |
| `code` | Kod autoryzacji zwrócony przez dostawcę tożsamości. |

Po odebraniu żądania tokenu dostawca tożsamości powinien odpowiedzieć przy użyciu tokenu identyfikatora.

```HTTP
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: no-store
Pragma: no-cache

{
"id_token": "eyJhbGciOiJSUzI1NiIsImtpZCI6IjFlOWdkazcifQ.ewogImlzc
    yI6ICJodHRwOi8vc2VydmVyLmV4YW1wbGUuY29tIiwKICJzdWIiOiAiMjQ4Mjg5
    NzYxMDAxIiwKICJhdWQiOiAiczZCaGRSa3F0MyIsCiAibm9uY2UiOiAibi0wUzZ
    fV3pBMk1qIiwKICJleHAiOiAxMzExMjgxOTcwLAogImlhdCI6IDEzMTEyODA5Nz
    AKfQ.ggW8hZ1EuVLuxNuuIJKX_V8a_OMXzR0EHR9R6jgdqrOOF4daGU96Sr_P6q
    Jp6IcmD3HP99Obi1PRs-cwh3LO-p146waJ8IhehcwL7F09JdijmBqkvPeB2T9CJ
    NqeGpe-gccMg4vfKjkM8FcGvnzZUN4_KSP0aAp1tOJ1zZwgjxqGByKHiOtX7Tpd
    QyHE5lcMiKPXfEIQILVq0pc_E2DzL7emopWoaoZTF_m0_N0YzFC6g6EJbOEoRoS
    K5hoDalrcvRYLSrQAZZKflyuVCyixEoV9GfNQC3_osjzw2PAithfubEEBLuVVk4
    XUVrWOLrLl0nx7RkKU8NXNHq-rvKMzqg"
}
```

Token identyfikatora musi używać formatu serializacji kompaktowania JWT i nie może być szyfrowany. Token identyfikatora powinien zawierać następujące oświadczenia.

| Claim | Wartość |
| ------- | ----------- |
| `kid` | Identyfikator klucza używanego do podpisywania tokenu identyfikatora odpowiadający wpisowi w dostawcy `jwks_uri` OpenID. |
| `aud` | Identyfikator klienta uzyskany podczas procesu rejestracji aplikacji. |
| `iss` | Musi być `issuer` wartością w dokumencie OpenID Connect konfiguracji. |
| `exp` | Musi zawierać czas wygaśnięcia tokenu identyfikatora. |
| `iat` | Musi zawierać czas, o którym wystawiono token identyfikatora. |
| `nonce` | Wartość uwzględniona w żądaniu autoryzacji. |
| Dodatkowe oświadczenia | Token identyfikatora powinien zawierać wszelkie dodatkowe oświadczenia, których wartości zostaną uwzględnione w wystawionym poświadczeniu weryfikowalnym. W tej sekcji należy uwzględnić wszelkie atrybuty dotyczące użytkownika, takie jak jego nazwa. |

## <a name="next-steps"></a>Następne kroki

- [Jak dostosować poświadczenia Azure Active Directory weryfikowalne](credential-design.md)
