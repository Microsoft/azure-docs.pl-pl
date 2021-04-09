---
title: Zdefiniuj profil techniczny OAuth2 w zasadach niestandardowych
titleSuffix: Azure AD B2C
description: Zdefiniuj profil techniczny OAuth2 w zasadach niestandardowych w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f79360269c19f6770fa12120ec34497b29015e7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99050689"
---
# <a name="define-an-oauth2-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Zdefiniuj profil techniczny OAuth2 w zasadach niestandardowych Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) zapewnia obsługę dostawcy tożsamości protokołu OAuth2. OAuth2 to podstawowy protokół do autoryzacji i uwierzytelniania delegowanego. Aby uzyskać więcej informacji, zobacz artykuł [RFC 6749 platformy autoryzacji OAuth 2,0](https://tools.ietf.org/html/rfc6749). Profil techniczny OAuth2 umożliwia sfederować z dostawcą tożsamości opartym na OAuth2, takim jak Facebook. Federowanie z dostawcą tożsamości umożliwia użytkownikom logowanie się przy użyciu istniejących tożsamości społecznościowych lub firmowych.

## <a name="protocol"></a>Protokół

Atrybut **name** elementu **Protocol** musi być ustawiony na `OAuth2` . Na przykład protokół dla profilu technicznego **Facebook-OAuth** `OAuth2` :

```xml
<TechnicalProfile Id="Facebook-OAUTH">
  <DisplayName>Facebook</DisplayName>
  <Protocol Name="OAuth2" />
  ...
```

## <a name="input-claims"></a>Oświadczenia wejściowe

Elementy **InputClaims** i **InputClaimsTransformations** nie są wymagane. Ale możesz chcieć wysłać dodatkowe parametry do dostawcy tożsamości. Poniższy przykład dodaje parametr **domain_hint** ciągu zapytania z wartością `contoso.com` do żądania autoryzacji.

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>Oświadczenia wyjściowe

Element **OutputClaims** zawiera listę oświadczeń zwracanych przez dostawcę tożsamości OAuth2. Może być konieczne zamapowanie nazwy żądania zdefiniowanego w zasadach na nazwę zdefiniowaną w dostawcy tożsamości. Można również uwzględnić oświadczenia, które nie są zwracane przez dostawcę tożsamości, o ile ustawisz `DefaultValue` atrybut.

Element **OutputClaimsTransformations** może zawierać kolekcję elementów **OutputClaimsTransformation** , które są używane do modyfikowania oświadczeń wyjściowych lub generowania nowych.

W poniższym przykładzie przedstawiono oświadczenia zwrócone przez dostawcę tożsamości w usłudze Facebook:

- **First_name** jest mapowany na **daną** wartość.
- **Last_name** jest mapowany do żądania **nazwisko** .
- Nazwa **wyświetlana** bez mapowania nazwy.
- Bez  mapowania nazwy.

Profil techniczny zwraca również oświadczenia, które nie są zwracane przez dostawcę tożsamości:

- **IdentityProvider** , który zawiera nazwę dostawcy tożsamości.
- **AuthenticationSource** z wartością domyślną **socialIdpAuthentication**.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
  <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
  <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="facebook.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Metadane

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| client_id | Tak | Identyfikator aplikacji dostawcy tożsamości. |
| IdTokenAudience | Nie | Odbiorcy id_token. Jeśli ta wartość jest określona, Azure AD B2C sprawdza, czy token znajduje się w ramach żądania zwróconego przez dostawcę tożsamości i jest równy określonemu. |
| authorization_endpoint | Tak | Adres URL punktu końcowego autoryzacji zgodnie z dokumentem RFC 6749. |
| AccessTokenEndpoint | Tak | Adres URL tokenu dla punktu końcowego w formacie RFC 6749. |
| ClaimsEndpoint | Tak | Adres URL punktu końcowego informacji o użytkowniku zgodnie z dokumentem RFC 6749. |
| end_session_endpoint | Tak | Adres URL punktu końcowego sesji końcowej zgodnie z dokumentem RFC 6749. |
| AccessTokenResponseFormat | Nie | Format wywołania punktu końcowego tokenu dostępu. Na przykład serwis Facebook wymaga metody HTTP GET, ale odpowiedź tokenu dostępu jest w formacie JSON. |
| AdditionalRequestQueryParameters | Nie | Dodatkowe parametry kwerendy żądania. Na przykład możesz chcieć wysłać dodatkowe parametry do dostawcy tożsamości. Można uwzględnić wiele parametrów przy użyciu ogranicznika przecinka. |
| ClaimsEndpointAccessTokenName | Nie | Nazwa parametru ciągu zapytania tokenu dostępu. Niektórzy punkty końcowe oświadczeń dostawców tożsamości obsługują pobieranie żądania HTTP. W takim przypadku token okaziciela jest wysyłany przy użyciu parametru ciągu zapytania zamiast nagłówka autoryzacji. Wartość domyślna: `access_token` . |
| ClaimsEndpointFormatName | Nie | Nazwa parametru ciągu zapytania. Na przykład możesz ustawić nazwę jako `format` w tym punkcie końcowym oświadczeń usługi LinkedIn `https://api.linkedin.com/v1/people/~?format=json` . |
| ClaimsEndpointFormat | Nie | Wartość parametru ciągu zapytania. Na przykład można ustawić wartość tak jak `json` w tym punkcie końcowym oświadczeń usługi LinkedIn `https://api.linkedin.com/v1/people/~?format=json` . |
| BearerTokenTransmissionMethod | Nie | Określa sposób wysyłania tokenu. Metoda domyślna jest ciągiem zapytania. Aby wysłać token jako nagłówek żądania, Ustaw jako `AuthorizationHeader` . |
| ProviderName | Nie | Nazwa dostawcy tożsamości. |
| response_mode | Nie | Metoda wykorzystywana przez dostawcę tożsamości do wysyłania wyniku z powrotem do Azure AD B2C. Możliwe wartości: `query` , `form_post` (wartość domyślna), lub `fragment` . |
| scope | Nie | Zakres żądania, który jest zdefiniowany zgodnie ze specyfikacją dostawcy tożsamości OAuth2. Takie jak `openid` , `profile` i `email` . |
| HttpBinding | Nie | Oczekiwano powiązania HTTP z punktami końcowymi tokenu dostępu i tokenów oświadczeń. Możliwe wartości: `GET` lub `POST` .  |
| ResponseErrorCodeParamName | Nie | Nazwa parametru, który zawiera komunikat o błędzie zwrócony za pośrednictwem protokołu HTTP 200 (ok). |
| ExtraParamsInAccessTokenEndpointResponse | Nie | Zawiera dodatkowe parametry, które mogą być zwracane w odpowiedzi z **AccessTokenEndpoint** przez niektórych dostawców tożsamości. Na przykład odpowiedź z **AccessTokenEndpoint** zawiera dodatkowy parametr, taki jak `openid` , który jest obowiązkowy parametr oprócz access_token w ciągu zapytania żądania **ClaimsEndpoint** . Nazwy wielu parametrów powinny być wyprowadzane i oddzielane przecinkami ",". |
| ExtraParamsInClaimsEndpointRequest | Nie | Zawiera dodatkowe parametry, które mogą być zwracane w żądaniu **ClaimsEndpoint** przez niektórych dostawców tożsamości. Nazwy wielu parametrów powinny być wyprowadzane i oddzielane przecinkami ",". |
| IncludeClaimResolvingInClaimsHandling  | Nie | W przypadku oświadczeń wejściowych i wyjściowych określa, czy w profilu technicznym znajduje się [rozpoznawanie oświadczeń](claim-resolver-overview.md) . Możliwe wartości: `true` , lub `false` (wartość domyślna). Jeśli chcesz użyć programu rozpoznawania oświadczeń w profilu technicznym, ustaw dla tej opcji wartość `true` . |
| ResolveJsonPathsInJsonTokens  | Nie | Wskazuje, czy profil techniczny rozwiązuje ścieżki JSON. Możliwe wartości: `true` , lub `false` (wartość domyślna). Użyj tych metadanych do odczytu danych z zagnieżdżonego elementu JSON. W [oświadczenie outputclaim](technicalprofiles.md#output-claims)Ustaw `PartnerClaimType` element na ścieżkę JSON, który ma zostać wyprowadzony. Na przykład: `firstName.localized` , lub `data.0.to.0.email` .|
|token_endpoint_auth_method| Nie| Określa sposób, w jaki Azure AD B2C wysyła nagłówek uwierzytelniania do punktu końcowego tokenu. Możliwe wartości: `client_secret_post` (ustawienie domyślne) i `client_secret_basic` (publiczna wersja zapoznawcza). Aby uzyskać więcej informacji, zobacz [sekcję OpenID Connect Connect Client Authentication](https://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication). |
|SingleLogoutEnabled| Nie| Wskazuje, czy podczas logowania profil techniczny próbuje wylogować się z federacyjnych dostawców tożsamości. Aby uzyskać więcej informacji, zobacz [Azure AD B2C wylogowania sesji](session-behavior.md#sign-out). Możliwe wartości: `true` (ustawienie domyślne) lub `false` .|
| UsePolicyInRedirectUri | Nie | Wskazuje, czy należy używać zasad podczas konstruowania identyfikatora URI przekierowania. Podczas konfigurowania aplikacji w dostawcy tożsamości należy określić identyfikator URI przekierowania. Identyfikator URI przekierowania wskazuje Azure AD B2C, `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp` . W przypadku określenia `true` tego elementu należy dodać identyfikator URI przekierowania dla każdej używanej zasady. Na przykład: `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/{policy-name}/oauth2/authresp`. |

## <a name="cryptographic-keys"></a>Klucze kryptograficzne

Element **CryptographicKeys** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| client_secret | Tak | Klucz tajny klienta aplikacji dostawcy tożsamości. Klucz kryptograficzny jest wymagany tylko wtedy, gdy metadane **response_types** są ustawione na `code` . W takim przypadku Azure AD B2C wykonuje inne wywołanie wymiany kodu autoryzacji dla tokenu dostępu. Jeśli metadane są ustawione na `id_token` , można pominąć klucz kryptograficzny. |

## <a name="redirect-uri"></a>Identyfikator URI przekierowania

Podczas konfigurowania identyfikatora URI przekierowania dostawcy tożsamości wprowadź wartość `https://{tenant-name}.b2clogin.com/{tenant-name}.onmicrosoft.com/oauth2/authresp` . Pamiętaj, aby zamienić na `{tenant-name}` nazwę dzierżawy (na przykład contosob2c). Identyfikator URI przekierowania musi zawierać tylko małe litery.

Przykłady:

- [Dodaj firmę Google + jako dostawcę tożsamości OAuth2 przy użyciu zasad niestandardowych](identity-provider-google.md)
