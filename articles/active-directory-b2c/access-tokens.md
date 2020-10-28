---
title: Żądanie tokenu dostępu — Azure Active Directory B2C | Microsoft Docs
description: Dowiedz się, jak zażądać tokenu dostępu z usługi Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/26/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 937041bbb48f112e2c8ed7d222dc7c7ef7ea8d81
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92631397"
---
# <a name="request-an-access-token-in-azure-active-directory-b2c"></a>Żądanie tokenu dostępu w usłudze Azure Active Directory B2C

*Token dostępu* zawiera oświadczenia, których można używać w usłudze Azure Active Directory B2C (Azure AD B2C) do identyfikowania uprawnień przyznanych interfejsom API. Podczas wywoływania serwera zasobów token dostępu musi znajdować się w żądaniu HTTP. Token dostępu jest oznaczany jako **access_token** w odpowiedziach z usługi Azure AD B2C.

W tym artykule pokazano, jak zażądać tokenu dostępu dla aplikacji internetowej i internetowego interfejsu API. Aby uzyskać więcej informacji na temat tokenów w usłudze Azure AD B2C, zobacz [omówienie tokenów w usłudze Azure Active Directory B2C](tokens-overview.md).

> [!NOTE]
> **Łańcuchy internetowych interfejsów API (przepływ On-Behalf-Of) nie są obsługiwane przez usługę Azure AD B2C.** Wiele architektur obejmuje internetowy interfejs API, który musi wywołać inny, podrzędny internetowy interfejs API, przy czym oba interfejsy są zabezpieczane przez usługę Azure AD B2C. Ten scenariusz jest typowy w przypadku klientów mających zaplecze w postaci internetowego interfejsu API, które z kolei wywołuje inną usługę. Ten scenariusz obejmujący łańcuch internetowych interfejsów API może być obsługiwany przy użyciu przyznania poświadczeń elementu nośnego OAuth 2.0 JWT, określanego również jako przepływ On-Behalf-Of. Jednak przepływ On-Behalf-Of nie jest obecnie zaimplementowany w usłudze Azure AD B2C.

## <a name="prerequisites"></a>Wymagania wstępne

- [Utwórz przepływ użytkownika](tutorial-create-user-flows.md), aby umożliwić użytkownikom rejestrowanie się w aplikacji i logowanie do niej.
- Jeśli jeszcze tego nie zrobiono, [dodaj aplikację internetowego interfejsu API do dzierżawy usługi Azure Active Directory B2C](add-web-api-application.md).

## <a name="scopes"></a>Zakresy

Zakresy umożliwiają zarządzanie uprawnieniami do chronionych zasobów. Po zażądaniu tokenu dostępu aplikacja kliencka musi określić żądane uprawnienia w parametrze **scope** żądania. Aby na przykład określić **Wartość zakresu** `read` dla interfejsu API, który ma **Identyfikator URI identyfikatora aplikacji** `https://contoso.onmicrosoft.com/api`, zakres to `https://contoso.onmicrosoft.com/api/read`.

Zakresy są używane przez internetowy interfejs API w celu implementowania kontroli dostępu opartej na zakresach. Na przykład użytkownicy internetowego interfejsu API mogą mieć dostęp zarówno do odczytu, jak i zapisu lub tylko dostęp do odczytu. Aby uzyskać wiele uprawnień w tym samym żądaniu, możesz dodać wiele rozdzielonych spacjami wpisów w pojedynczym parametrze **scope** żądania.

Poniższy przykład przedstawia zakresy zdekodowane w adresie URL:

```
scope=https://contoso.onmicrosoft.com/api/read openid offline_access
```

Poniższy przykład przedstawia zakresy zakodowane w adresie URL:

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fapi%2Fread%20openid%20offline_access
```

Jeśli zażądasz więcej zakresów niż przyznano aplikacji klienckiej, wywołanie zakończy się powodzeniem w przypadku przyznania co najmniej jednego uprawnienia. Oświadczenie **scp** w wynikowym tokenie dostępu zostanie wypełnione tylko uprawnieniami, które zostały pomyślnie przyznane. 

### <a name="openid-connect-scopes"></a>Zakresy połączeń OpenID Connect

Standard OpenID Connect określa kilka specjalnych wartości zakresów. Następujące zakresy reprezentują uprawnienie dostępu do profilu użytkownika:

- **openid** — żąda tokenu identyfikatora.
- **offline_access** — żąda tokenu odświeżania przy użyciu [przepływów kodu uwierzytelniania](authorization-code-flow.md).
- **00000000-0000-0000-0000-000000000000** — użycie identyfikatora klienta jako zakresu wskazuje, że aplikacja wymaga tokenu dostępu, który może być używany w odniesieniu do własnej usługi lub internetowego interfejsu API, reprezentowanego przez ten sam identyfikator klienta.

Jeśli parametr **response_type** w żądaniu `/authorize` zawiera element `token`, parametr **scope** musi zawierać co najmniej jeden zakres zasobów inny niż `openid` i `offline_access`, który zostanie przyznany. W przeciwnym razie żądanie `/authorize` zakończy się niepowodzeniem.

## <a name="request-a-token"></a>Żądanie tokenu

Aby zażądać tokenu dostępu, musisz mieć kod autoryzacji. Poniżej znajduje się przykład żądania kodu autoryzacji wysyłanego do punktu końcowego `/authorize`. Użycie domen niestandardowych w przypadku tokenów dostępu nie jest obsługiwane. W adresie URL żądania podaj domenę nazwa-dzierżawy.onmicrosoft.com.

W poniższym przykładzie zastąp następujące wartości:

- `<tenant-name>` — nazwa domeny dzierżawy usługi Azure AD B2C.
- `<policy-name>` — nazwa zasad niestandardowych lub przepływu użytkownika.
- `<application-ID>` — identyfikator aplikacji internetowej, która została zarejestrowana w celu obsługi przepływu użytkownika.
- `<redirect-uri>` — **Identyfikator URI przekierowania** wprowadzony podczas rejestrowania aplikacji klienckiej.

```http
GET https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize?
client_id=<application-ID>
&nonce=anyRandomValue
&redirect_uri=https://jwt.ms
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&response_type=code
```

Odpowiedź z kodem autoryzacji powinna być podobna do następującego przykładu:

```
https://jwt.ms/?code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
```

Po pomyślnym otrzymaniu kodu autoryzacji można go użyć do zażądania tokenu dostępu:

```http
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

Powinna zostać zwrócona odpowiedź podobna do poniższej:

```json
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

Po zbadaniu zwróconego tokenu dostępu w witrynie https://jwt.ms powinny zostać wyświetlone dane podobne do następującego przykładu:

```json
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

- Dowiedz się więcej na temat [konfigurowania tokenów w usłudze Azure AD B2C](configure-tokens.md)
