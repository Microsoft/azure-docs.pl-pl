---
title: Żądanie tokenu dostępu — Azure Active Directory B2C | Microsoft Docs
description: Dowiedz się, jak zażądać tokenu dostępu z Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 36027583d64ac91432888d866440932c6e1bdd07
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83635442"
---
# <a name="request-an-access-token-in-azure-active-directory-b2c"></a>Żądanie tokenu dostępu w Azure Active Directory B2C

*Token dostępu* zawiera oświadczenia, których można użyć w Azure Active Directory B2C (Azure AD B2C) do zidentyfikowania przyznanych uprawnień do interfejsów API. Podczas wywoływania serwera zasobów token dostępu musi znajdować się w żądaniu HTTP. Token dostępu jest oznaczany jako **access_token** w odpowiedziach z Azure AD B2C.

W tym artykule pokazano, jak zażądać tokenu dostępu dla aplikacji sieci Web i internetowego interfejsu API. Aby uzyskać więcej informacji na temat tokenów w Azure AD B2C, zobacz [Omówienie tokenów w programie Azure Active Directory B2C](tokens-overview.md).

> [!NOTE]
> **Łańcuchy interfejsu API sieci Web (w imieniu) nie są obsługiwane przez Azure AD B2C.** — Wiele architektur obejmuje internetowy interfejs API, który musi wywołać inny podrzędny interfejs API sieci Web, zabezpieczony przez Azure AD B2C. Ten scenariusz jest typowy w przypadku klientów mających zaplecze interfejsu API sieci Web, co z kolei wywołuje inną usługę. Ten scenariusz łańcuchowego interfejsu API sieci Web może być obsługiwany przy użyciu dotacji do autoryzacji OAuth 2,0 JWT, w przeciwnym razie nazywanego przepływem. Jednak przepływ w imieniu nie jest obecnie zaimplementowany w Azure AD B2C.

## <a name="prerequisites"></a>Wymagania wstępne

- [Utwórz przepływ użytkownika](tutorial-create-user-flows.md) , aby umożliwić użytkownikom rejestrowanie się w aplikacji i logowanie się do niej.
- Jeśli jeszcze tego nie zrobiono, [Dodaj aplikację internetowego interfejsu API do dzierżawy Azure Active Directory B2C](add-web-application.md).

## <a name="scopes"></a>Zakresy

Zakresy umożliwiają zarządzanie uprawnieniami do chronionych zasobów. Po zażądaniu tokenu dostępu aplikacja kliencka musi określić odpowiednie uprawnienia w parametrze **zakresu** żądania. Na przykład, aby określić **wartość zakresu** `read` dla interfejsu API, który ma **Identyfikator URI aplikacji** `https://contoso.onmicrosoft.com/api` , zakresem będzie `https://contoso.onmicrosoft.com/api/read` .

Zakresy są używane przez internetowy interfejs API w celu implementowania kontroli dostępu opartej na zakresach. Na przykład użytkownicy internetowego interfejsu API mogą mieć dostęp zarówno do odczytu, jak i zapisu lub tylko dostęp do odczytu. Aby uzyskać wiele uprawnień w tym samym żądaniu, można dodać wiele wpisów **w pojedynczym** parametrze żądania, rozdzielone spacjami.

W poniższym przykładzie przedstawiono zakresy zdekodowane w adresie URL:

```
scope=https://contoso.onmicrosoft.com/api/read openid offline_access
```

Poniższy przykład pokazuje zakresy zakodowane w adresie URL:

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fapi%2Fread%20openid%20offline_access
```

Jeśli zażądasz więcej zakresów niż to, co jest przyznane aplikacji klienckiej, wywołanie powiedzie się, Jeśli przyznano co najmniej jedno uprawnienie. W przypadku wystąpienia usługi **SCP** w uzyskanym tokenie dostępu zostanie wypełniony tylko pomyślnie przyznane uprawnienia. Standard OpenID Connect Connect określa kilka specjalnych wartości zakresu. Następujące zakresy reprezentują uprawnienia dostępu do profilu użytkownika:

- **OpenID Connect** — żąda tokenu identyfikatora.
- **offline_access** — żąda tokenu odświeżania przy użyciu [przepływów kodu uwierzytelniania](authorization-code-flow.md).

Jeśli parametr **response_type** w `/authorize` żądaniu zawiera `token` , parametr **SCOPE** musi zawierać co najmniej jeden zakres zasobów inny niż `openid` i `offline_access` , który zostanie przyznany. W przeciwnym razie `/authorize` żądanie zakończy się niepowodzeniem.

## <a name="request-a-token"></a>Żądanie tokenu

Aby zażądać tokenu dostępu, musisz mieć kod autoryzacji. Poniżej znajduje się przykład żądania do `/authorize` punktu końcowego kodu autoryzacji. Domeny niestandardowe nie są obsługiwane w przypadku tokenów dostępu. Użyj domeny tenant-name.onmicrosoft.com w adresie URL żądania.

W poniższym przykładzie zastąpisz następujące wartości:

- `<tenant-name>`— Nazwa dzierżawy Azure AD B2C.
- `<policy-name>`— Nazwa zasad niestandardowych lub przepływu użytkownika.
- `<application-ID>`— Identyfikator aplikacji sieci Web, która została zarejestrowana w celu obsługi przepływu użytkownika.
- `<redirect-uri>`— **Identyfikator URI przekierowania** wprowadzony podczas rejestrowania aplikacji klienckiej.

```HTTP
GET https://<tenant-name>.b2clogin.com/tfp/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize?
client_id=<application-ID>
&nonce=anyRandomValue
&redirect_uri=https://jwt.ms
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&response_type=code
```

Odpowiedź z kodem autoryzacji powinna być podobna do tego przykładu:

```
https://jwt.ms/?code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
```

Po pomyślnym otrzymaniu kodu autoryzacji można użyć go do żądania tokenu dostępu:

```HTTP
POST <tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/token HTTP/1.1
Host: <tenant-name>.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code
&client_id=<application-ID>
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
&redirect_uri=https://jwt.ms
&client_secret=2hMG2-_:y12n10vwH...
```

Powinien zostać wyświetlony komunikat podobny do następującego:

```JSON
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrN...",
    "token_type": "Bearer",
    "not_before": 1549647431,
    "expires_in": 3600,
    "expires_on": 1549651031,
    "resource": "f2a76e08-93f2-4350-833c-965c02483b11",
    "profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiJjNjRhNGY3ZC0zMDkxLTRjNzMtYTcyMi1hM2YwNjk0Z..."
}
```

W przypadku użycia https://jwt.ms do badania zwróconego tokenu dostępu powinien zostać wyświetlony komunikat podobny do następującego:

```JSON
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dl..."
}.{
  "iss": "https://contoso0926tenant.b2clogin.com/c64a4f7d-3091-4c73-a7.../v2.0/",
  "exp": 1549651031,
  "nbf": 1549647431,
  "aud": "f2a76e08-93f2-4350-833c-965...",
  "oid": "1558f87f-452b-4757-bcd1-883...",
  "sub": "1558f87f-452b-4757-bcd1-883...",
  "name": "David",
  "tfp": "B2C_1_signupsignin1",
  "nonce": "anyRandomValue",
  "scp": "read",
  "azp": "38307aee-303c-4fff-8087-d8d2...",
  "ver": "1.0",
  "iat": 1549647431
}.[Signature]
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [konfigurowania tokenów w Azure AD B2C](configure-tokens.md)
