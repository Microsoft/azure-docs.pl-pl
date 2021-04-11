---
title: Przykłady komunikacji usługi wystawcy (wersja zapoznawcza) — Azure Active Directory poświadczenia do zweryfikowania
description: Szczegóły komunikacji między dostawcą tożsamości a usługą wystawcy
author: barclayn
manager: davba
ms.service: identity
ms.subservice: verifiable-credentials
ms.workload: identity
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 8771c61f96b244e0cc0bca1c61ceb8042b4a5b4c
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220202"
---
# <a name="issuer-service-communication-examples-preview"></a>Przykłady komunikacji z usługą wystawcy (wersja zapoznawcza)

Zweryfikowana usługa wystawcy poświadczeń może wydać zweryfikowane poświadczenia przez pobranie oświadczeń z tokenu identyfikatora wygenerowanego przez dostawcę tożsamości zgodnego z OpenID connectą w organizacji. W tym artykule zawarto instrukcje dotyczące konfigurowania dostawcy tożsamości, aby umożliwić mu komunikowanie się z nim i pobrać prawidłowy token IDENTYFIKACYJNy do przekazania do usługi wystawiania. 

> [!IMPORTANT]
> Azure Active Directory poświadczenia do zweryfikowania są obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Aby możliwe było zweryfikowanie poświadczenia, wystawca uwierzytelnienia jest podawany przez pobranie kontraktu w celu zebrania danych wejściowych od użytkownika i wysłania tych informacji do usługi wystawiania. Jeśli musisz użyć tokenu identyfikatora, musisz skonfigurować dostawcę tożsamości, aby umożliwić uwierzytelniającego Logowanie użytkownika przy użyciu protokołu Connect OpenID Connect. Oświadczenia w tokenie będącym wynikiem ID są używane do wypełniania zawartości do zweryfikowanego poświadczenia. Wystawca uwierzytelnienia użytkownika przy użyciu przepływu kodu autoryzacji OpenID Connect Connect. Dostawca OpenID Connect musi obsługiwać następujące funkcje połączenia OpenID Connect: 

| Cecha | Opis |
| ------- | ----------- |
| Typ udzielania | Musi obsługiwać typ udzielania kodu autoryzacji. |
| Format tokenu | Musi generować nieszyfrowane kompakty JWTs. |
| Algorytm podpisu | Musi generować JWTs podpisane przy użyciu RSA 256. |
| Dokument konfiguracyjny | Musi obsługiwać dokument konfiguracji OpenID Connect Connect i `jwks_uri` . | 
| Rejestracja klienta | Musi obsługiwać publiczną rejestrację klienta przy użyciu `redirect_uri` wartości `vclient://openid/` . | 
| PKCE | Zalecane ze względów bezpieczeństwa, ale nie są wymagane. |

Poniżej znajdują się przykłady żądań HTTP wysyłanych do dostawcy tożsamości. Dostawca tożsamości musi akceptować te żądania i odpowiadać na nie zgodnie ze standardem uwierzytelniania OpenID Connect Connect.

## <a name="client-registration"></a>Rejestracja klienta

Aby otrzymywać zweryfikowane poświadczenia, użytkownicy muszą się zalogować do dostawcy tożsamości z aplikacji Microsoft Authenticator. 

Aby włączyć tę wymianę, zarejestruj aplikację u dostawcy tożsamości. Jeśli używasz usługi Azure AD, możesz znaleźć instrukcje w [tym miejscu](../develop/quickstart-register-app.md). Podczas rejestrowania należy użyć następujących wartości.

| Ustawienie | Wartość |
| ------- | ----- |
| Nazwa aplikacji | `<Issuer Name> Verifiable Credential Service` |
| Identyfikator URI przekierowania | `vcclient://openid/ ` |


Po zarejestrowaniu aplikacji u dostawcy tożsamości Zapisz jej identyfikator klienta. Zostanie ona użyta w poniższej sekcji. Należy również napisać adres URL do dobrze znanego punktu końcowego dla dostawcy tożsamości zgodnego z OIDC. Usługa wystawiania używa tego punktu końcowego do pobierania kluczy publicznych wymaganych do zweryfikowania tokenu identyfikatora, gdy zostanie on wysłany przez wystawcę uwierzytelnienia.

Skonfigurowany identyfikator URI przekierowania jest używany przez wystawcę uwierzytelnienia, więc wie, że logowanie zostało zakończone i może pobrać token identyfikatora. 

## <a name="authorization-request"></a>Żądanie autoryzacji

Żądanie autoryzacji wysłane do dostawcy tożsamości ma następujący format.

```HTTP
GET /authorize?client_id=<client-id>&redirect_uri=portableidentity%3A%2F%2Fverify&response_mode=query&response_type=code&scope=openid&state=12345&nonce=12345 HTTP/1.1
Host: www.contoso.com
Connection: Keep-Alive
```

| Parametr | Wartość |
| ------- | ----------- |
| `client_id` | Identyfikator klienta uzyskany podczas procesu rejestracji aplikacji. |
| `redirect_uri` | Musi używać `vcclient://openid/` . |
| `response_mode` | Musi obsługiwać `query` . |
| `response_type` | Musi obsługiwać `code` . |
| `scope` | Musi obsługiwać `openid` . |
| `state` | Musi być zwracany do klienta zgodnie ze standardem OpenID Connect Connect. |
| `nonce` | Musi być zwracany jako element Claim w tokenie ID zgodnie ze standardem OpenID Connect Connect. |

Gdy odbierze żądanie autoryzacji, dostawca tożsamości powinien uwierzytelnić użytkownika i wykonać wszelkie kroki niezbędne do ukończenia logowania, takie jak uwierzytelnianie wieloskładnikowe.

Proces logowania można dostosować do własnych potrzeb. Można zażądać od użytkowników podania dodatkowych informacji, zaakceptowania warunków użytkowania, zaliczania za poświadczenia i nie tylko. Po zakończeniu wszystkich kroków należy odpowiedzieć na żądanie autoryzacji, przekierowując do identyfikatora URI przekierowania, jak pokazano poniżej. 

```HTTP
vcclient://openid/?code=nbafhjbh1ub1yhbj1h4jr1&state=12345
```

| Parametr | Wartość |
| ------- | ----------- |
| `code` |  Kod autoryzacji zwrócony przez dostawcę tożsamości. |
| `state` | Musi być zwracany do klienta zgodnie ze standardem OpenID Connect Connect. |

## <a name="token-request"></a>Żądanie tokenu

Żądanie tokenu wysłane do dostawcy tożsamości będzie miało następującą formę:.

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
| `redirect_uri` | Musi używać `vcclient://openid/` . |
| `scope` | Musi obsługiwać `openid` . |
| `grant_type` | Musi obsługiwać `authorization_code` . |
| `code` | Kod autoryzacji zwrócony przez dostawcę tożsamości. |

Po otrzymaniu żądania tokenu dostawca tożsamości powinien odpowiedzieć z tokenem ID.

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

Token identyfikatora musi używać formatu serializacji kompaktowej JWT i nie może być zaszyfrowany. Token identyfikatora powinien zawierać następujące oświadczenia.

| Claim | Wartość |
| ------- | ----------- |
| `kid` | Identyfikator klucza służący do podpisywania tokenu identyfikatora, odpowiadający wpisowi w dostawcy OpenID Connect `jwks_uri` . |
| `aud` | Identyfikator klienta uzyskany podczas procesu rejestracji aplikacji. |
| `iss` | Musi być `issuer` wartością w dokumencie konfiguracji OpenID Connect Connect. |
| `exp` | Musi zawierać czas wygaśnięcia tokenu identyfikatora. |
| `iat` | Musi zawierać czas, w którym został wystawiony token ID. |
| `nonce` | Wartość uwzględniona w żądaniu autoryzacji. |
| Dodatkowe oświadczenia | Token identyfikatora powinien zawierać dodatkowe oświadczenia, których wartości zostaną uwzględnione w poświadczeniach, które zostaną wystawione. W tej sekcji należy uwzględnić wszelkie atrybuty dotyczące użytkownika, takie jak ich nazwy. |

## <a name="next-steps"></a>Następne kroki

- [Jak dostosować Azure Active Directory poświadczenia do zweryfikowania](credential-design.md)