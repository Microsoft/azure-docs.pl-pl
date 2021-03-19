---
title: Przewodnik migracji Python ADAL do MSAL | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak przeprowadzić migrację aplikacji w języku Python biblioteki Azure Active Directory Authentication Library (ADAL) do biblioteki uwierzytelniania firmy Microsoft (MSAL) dla języka Python.
services: active-directory
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Python
ms.workload: identity
ms.date: 11/11/2019
ms.author: rayluo
ms.reviewer: marsma, rayluo, nacanuma
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 60d6e40b568c1189cdc01ef4239612d3717063b7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578842"
---
# <a name="adal-to-msal-migration-guide-for-python"></a>Przewodnik migracji biblioteki ADAL do MSAL dla języka Python

W tym artykule przedstawiono zmiany, które należy wykonać w celu przeprowadzenia migracji aplikacji, która Azure Active Directory używa biblioteki MSAL Authentication Library (ADAL) do korzystania z biblioteki uwierzytelniania firmy Microsoft.

## <a name="difference-highlights"></a>Różnice między wyróżnieniami

Biblioteka ADAL współpracuje z punktem końcowym Azure Active Directory (Azure AD) v 1.0. Biblioteka Microsoft Authentication Library (MSAL) współpracuje z platformą tożsamości firmy Microsoft — znana wcześniej jako punkt końcowy Azure Active Directory v 2.0. Platforma tożsamości firmy Microsoft różni się od usługi Azure AD v 1.0:

Uguje
  - Konta służbowe (konta aprowizacji usługi Azure AD)
  - Konta osobiste (takie jak Outlook.com lub Hotmail.com)
  - Klienci, którzy korzystają z własnej poczty e-mail lub tożsamości społecznościowej (na przykład LinkedIn, Facebook, Google) za pośrednictwem oferty Azure AD B2C

- Są zgodne ze standardami:
  - OAuth 2.0
  - OpenID Connect Connect (OIDC)

Zapoznaj [się z informacjami o platformie tożsamości firmy Microsoft?,](../azuread-dev/azure-ad-endpoint-comparison.md) Aby uzyskać więcej szczegółów.

### <a name="scopes-not-resources"></a>Zakresy nie są zasobami

Biblioteka ADAL Python uzyskuje tokeny dla zasobów, ale MSAL Python uzyskuje tokeny dla zakresów. Powierzchnia interfejsu API w MSAL Python nie ma już parametru zasobu. Należy podać zakresy jako listę ciągów, które deklarują żądane uprawnienia i żądane zasoby. Aby zobaczyć przykład zakresów, zobacz [zakresy Microsoft Graph](/graph/permissions-reference).

Możesz dodać `/.default` sufiks zakresu do zasobu, aby ułatwić migrację aplikacji z punktu końcowego v 1.0 (ADAL) do platformy tożsamości firmy Microsoft (MSAL). Na przykład dla wartości zasobu wartość `https://graph.microsoft.com` równoważna wartość zakresu to `https://graph.microsoft.com/.default` .  Jeśli zasób nie jest w formie adresu URL, ale identyfikator zasobu formularza `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX` , można nadal używać wartości zakresu jako `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX/.default` .

Aby uzyskać więcej informacji na temat różnych typów zakresów, zapoznaj się z [uprawnieniami i wyrażaniem zgody na platformie tożsamości firmy Microsoft](./v2-permissions-and-consent.md) oraz [zakresami dla internetowego interfejsu API akceptujących artykuły tokenów w wersji 1.0](./msal-v1-app-scopes.md) .

### <a name="error-handling"></a>Obsługa błędów

Biblioteka Azure Active Directory Authentication Library (ADAL) dla języka Python używa wyjątku, `AdalError` Aby wskazać, że wystąpił problem. MSAL for Python zwykle używa kodów błędów zamiast. Aby uzyskać więcej informacji, zobacz [MSAL for Python — obsługa błędów](msal-error-handling-python.md).

### <a name="api-changes"></a>Zmiany interfejsu API

Poniższa tabela zawiera listę interfejsów API w bibliotece ADAL dla języka Python oraz te, które mają być używane w środowisku MSAL dla języka Python:

| Biblioteka ADAL dla interfejsu API języka Python  | MSAL dla interfejsu API języka Python |
| ------------------- | ---------------------------------- |
| [AuthenticationContext](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext)  | [PublicClientApplication lub ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__)  |
| Nie dotyczy  | [get_authorization_request_url ()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.get_authorization_request_url)  |
| [acquire_token_with_authorization_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_authorization_code) | [acquire_token_by_authorization_code ()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_by_authorization_code) |
| [acquire_token ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token) | [acquire_token_silent ()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_silent) |
| [acquire_token_with_refresh_token ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_refresh_token) | Nie dotyczy |
| [acquire_user_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_user_code) | [initiate_device_flow ()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.initiate_device_flow) |
| [acquire_token_with_device_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_device_code) i [cancel_request_to_get_token_with_device_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.cancel_request_to_get_token_with_device_code) | [acquire_token_by_device_flow ()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow) |
| [acquire_token_with_username_password ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_username_password) | [acquire_token_by_username_password ()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_username_password) |
| [acquire_token_with_client_credentials ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_credentials) i [acquire_token_with_client_certificate ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_certificate) | [acquire_token_for_client ()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client) |
| Nie dotyczy | [acquire_token_on_behalf_of ()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) |
| [TokenCache ()](https://adal-python.readthedocs.io/en/latest/#adal.TokenCache) | [SerializableTokenCache()](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) |
| Nie dotyczy | Pamięć podręczna z trwałość, dostępna z poziomu [rozszerzeń MSAL](https://github.com/marstr/original-microsoft-authentication-extensions-for-python) |

## <a name="migrate-existing-refresh-tokens-for-msal-python"></a>Migrowanie istniejących tokenów odświeżania dla języka MSAL Python

Biblioteka Microsoft Authentication Library (MSAL) stanowi streszczenie koncepcji tokenów odświeżania. MSAL Python domyślnie udostępnia pamięć podręczną tokenów w pamięci, dzięki czemu nie trzeba przechowywać, wyszukiwać ani aktualizować tokenów odświeżania. Użytkownicy będą również widzieć mniejszą liczbę monitów logowania, ponieważ tokeny odświeżania mogą być zazwyczaj aktualizowane bez interwencji użytkownika. Aby uzyskać więcej informacji na temat pamięci podręcznej tokenów, zobacz [niestandardowe serializacji pamięci podręcznej w MSAL dla języka Python](msal-python-token-cache-serialization.md).

Poniższy kod pomoże Ci migrować tokeny odświeżania zarządzane przez inną bibliotekę OAuth2 (w tym, ale nie ograniczone do biblioteki ADAL Python), aby były zarządzane przez MSAL dla języka Python. Jedną z przyczyn migrowania tych tokenów odświeżania jest uniemożliwienie zalogowania się istniejących użytkowników podczas migrowania aplikacji do MSAL dla języka Python.

Metoda migrowania tokenu odświeżania polega na użyciu MSAL dla języka Python w celu uzyskania nowego tokenu dostępu przy użyciu poprzedniego tokenu odświeżania. Po zwróceniu nowego tokenu odświeżania MSAL dla języka Python zapisze go w pamięci podręcznej.
Ponieważ MSAL Python 1.3.0, udostępniamy interfejs API wewnątrz MSAL do tego celu.
Zapoznaj się z poniższym fragmentem kodu, cytowanym z [kompletnego przykładu migrowania tokenów odświeżania przy użyciu języka MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.3.0/sample/migrate_rt.py#L28-L67)

```python
import msal
def get_preexisting_rt_and_their_scopes_from_elsewhere():
    # Maybe you have an ADAL-powered app like this
    #   https://github.com/AzureAD/azure-activedirectory-library-for-python/blob/1.2.3/sample/device_code_sample.py#L72
    # which uses a resource rather than a scope,
    # you need to convert your v1 resource into v2 scopes
    # See https://docs.microsoft.com/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources
    # You may be able to append "/.default" to your v1 resource to form a scope
    # See https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#the-default-scope

    # Or maybe you have an app already talking to the Microsoft identity platform,
    # powered by some 3rd-party auth library, and persist its tokens somehow.

    # Either way, you need to extract RTs from there, and return them like this.
    return [
        ("old_rt_1", ["scope1", "scope2"]),
        ("old_rt_2", ["scope3", "scope4"]),
        ]


# We will migrate all the old RTs into a new app powered by MSAL
app = msal.PublicClientApplication(
    "client_id", authority="...",
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache
    )

# We choose a migration strategy of migrating all RTs in one loop
for old_rt, scopes in get_preexisting_rt_and_their_scopes_from_elsewhere():
    result = app.acquire_token_by_refresh_token(old_rt, scopes)
    if "error" in result:
        print("Discarding unsuccessful RT. Error: ", json.dumps(result, indent=2))

print("Migration completed")
```


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [porównanie v 1.0 i 2.0](../azuread-dev/azure-ad-endpoint-comparison.md).
