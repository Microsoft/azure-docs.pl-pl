---
title: Sprawdzanie poprawności tokenu dostępu usługi Azure API for FHIR
description: Przeprowadzi weryfikację tokenu i zawiera porady dotyczące rozwiązywania problemów z dostępem
services: healthcare-apis
author: caitlinv39
ms.reviewer: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: cavoeg
ms.openlocfilehash: 426ec0c2d6cc274aa0b6829eb4a30fd29b9ba8e2
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103020275"
---
# <a name="azure-api-for-fhir-access-token-validation"></a>Sprawdzanie poprawności tokenu dostępu usługi Azure API for FHIR

Jak usługa Azure API for FHIR sprawdza poprawność tokenu dostępu, będzie zależeć od implementacji i konfiguracji. W tym artykule przedstawiono kroki weryfikacji, które mogą być przydatne podczas rozwiązywania problemów z dostępem.

## <a name="validate-token-has-no-issues-with-identity-provider"></a>Walidacja tokenu nie ma żadnych problemów z dostawcą tożsamości

Pierwszym krokiem weryfikacji tokenu jest sprawdzenie, czy token został wystawiony przez poprawnego dostawcę tożsamości i nie został zmodyfikowany. Serwer FHIR zostanie skonfigurowany do korzystania z określonego dostawcy tożsamości znanego jako urząd `Authority` . Serwer FHIR będzie pobierał informacje o dostawcy tożsamości z `/.well-known/openid-configuration` punktu końcowego. W przypadku korzystania z usługi Azure AD pełny adres URL to:

```
GET https://login.microsoftonline.com/<TENANT-ID>/.well-known/openid-configuration
```

gdzie `<TENANT-ID>` jest określona dzierżawa usługi Azure AD (identyfikator dzierżawy lub nazwa domeny).

Usługa Azure AD zwróci dokument podobny do przedstawionego poniżej na serwerze FHIR.

```json
{
    "authorization_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize",
    "token_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/token",
    "token_endpoint_auth_methods_supported": [
        "client_secret_post",
        "private_key_jwt",
        "client_secret_basic"
    ],
    "jwks_uri": "https://login.microsoftonline.com/common/discovery/keys",
    "response_modes_supported": [
        "query",
        "fragment",
        "form_post"
    ],
    "subject_types_supported": [
        "pairwise"
    ],
    "id_token_signing_alg_values_supported": [
        "RS256"
    ],
    "http_logout_supported": true,
    "frontchannel_logout_supported": true,
    "end_session_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/logout",
    "response_types_supported": [
        "code",
        "id_token",
        "code id_token",
        "token id_token",
        "token"
    ],
    "scopes_supported": [
        "openid"
    ],
    "issuer": "https://sts.windows.net/<TENANT-ID>/",
    "claims_supported": [
        "sub",
        "iss",
        "cloud_instance_name",
        "cloud_instance_host_name",
        "cloud_graph_host_name",
        "msgraph_host",
        "aud",
        "exp",
        "iat",
        "auth_time",
        "acr",
        "amr",
        "nonce",
        "email",
        "given_name",
        "family_name",
        "nickname"
    ],
    "microsoft_multi_refresh_token": true,
    "check_session_iframe": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/checksession",
    "userinfo_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/openid/userinfo",
    "tenant_region_scope": "WW",
    "cloud_instance_name": "microsoftonline.com",
    "cloud_graph_host_name": "graph.windows.net",
    "msgraph_host": "graph.microsoft.com",
    "rbac_url": "https://pas.windows.net"
}
``` 
Ważne właściwości serwera FHIR to `jwks_uri` , który informuje serwer, gdzie należy pobrać klucze szyfrowania potrzebne do zweryfikowania podpisu tokenu i `issuer` , który informuje serwer, który będzie znajdować się w ramach roszczeń wystawcy ( `iss` ) tokenów wystawionych przez ten serwer. Serwer FHIR może użyć tego do zweryfikowania, czy otrzymuje on autentyczny token.

## <a name="validate-claims-of-the-token"></a>Weryfikowanie oświadczeń tokenu

Po zweryfikowaniu autentyczności tokenu serwer FHIR będzie kontynuował sprawdzanie, czy klient ma wymagane oświadczenia, aby uzyskać dostęp do tokenu.

W przypadku korzystania z interfejsu API platformy Azure dla usługi FHIR serwer sprawdzi poprawność:

1. Token ma prawo `Audience` ( `aud` Claim).
1. Użytkownik lub podmiot zabezpieczeń, dla którego token został wystawiony, mogą uzyskać dostęp do płaszczyzny danych serwera FHIR. W ramach `oid` żądania tokenu znajduje się identyfikator obiektu tożsamości, który jednoznacznie identyfikuje użytkownika lub podmiot zabezpieczeń.

Firma Microsoft zaleca, aby usługa FHIR była skonfigurowana do zarządzania przypisaniami ról płaszczyzny danych przy [użyciu kontroli RBAC platformy Azure](configure-azure-rbac.md) . Możesz również [skonfigurować lokalne RBAC](configure-local-rbac.md) , jeśli usługa FHIR korzysta z dzierżawy Azure Active Directory zewnętrznej lub pomocniczej. 

W przypadku korzystania z serwera OSS Microsoft FHIR Server dla platformy Azure serwer sprawdzi poprawność:

1. Token ma prawo `Audience` ( `aud` Claim).
1. Token ma rolę w ramach tego `roles` żądania, która ma dostęp do serwera FHIR.

Zapoznaj się ze szczegółami dotyczącymi sposobu [definiowania ról na serwerze FHIR](https://github.com/microsoft/fhir-server/blob/master/docs/Roles.md).

Serwer FHIR może również sprawdzić, czy token dostępu ma zakresy (w ramach żądania tokenu `scp` ), aby uzyskać dostęp do części interfejsu API FHIR, do którego klient próbuje uzyskać dostęp. Obecnie usługa Azure API for FHIR i serwer FHIR dla platformy Azure nie weryfikują zakresów tokenów.

## <a name="next-steps"></a>Następne kroki
Teraz, gdy wiesz już, jak sprawdzać poprawność tokenów, możesz wykonać ten samouczek, aby utworzyć aplikację JavaScript i odczytać dane FHIR.

>[!div class="nextstepaction"]
>[Samouczek aplikacji sieci Web](tutorial-web-app-fhir-server.md)