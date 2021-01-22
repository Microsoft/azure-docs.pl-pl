---
title: Definiowanie profilu technicznego OpenID Connect Connect w zasadach niestandardowych
titleSuffix: Azure AD B2C
description: Zdefiniuj profil techniczny OpenID Connect Connect w zasadach niestandardowych w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/01/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f6b6fb18ce086c2eadc829f03460452deb0a12b9
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98675156"
---
# <a name="define-an-openid-connect-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Zdefiniuj profil techniczny OpenID Connect Connect w zasadach niestandardowych Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) zapewnia obsługę dostawcy tożsamości protokołu [OpenID Connect Connect](https://openid.net/2015/04/17/openid-connect-certification-program/) . OpenID Connect Connect 1,0 definiuje warstwę tożsamości na serwerze OAuth 2,0 i reprezentuje stan grafiki w nowoczesnych protokołach uwierzytelniania. Za pomocą profilu technicznego OpenID Connect Connect można sfederować przy użyciu dostawcy tożsamości usługi OpenID Connect Connect, takiego jak Azure AD. Federowanie z dostawcą tożsamości umożliwia użytkownikom logowanie się przy użyciu istniejących tożsamości społecznościowych lub firmowych.

## <a name="protocol"></a>Protokół

Atrybut **name** elementu **Protocol** musi być ustawiony na `OpenIdConnect` . Na przykład protokół dla profilu technicznego **MSA-OIDC** `OpenIdConnect` :

```xml
<TechnicalProfile Id="MSA-OIDC">
  <DisplayName>Microsoft Account</DisplayName>
  <Protocol Name="OpenIdConnect" />
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

Element **OutputClaims** zawiera listę oświadczeń zwracanych przez dostawcę tożsamości programu OpenID Connect Connect. Może być konieczne zamapowanie nazwy żądania zdefiniowanego w zasadach na nazwę zdefiniowaną w dostawcy tożsamości. Można również uwzględnić oświadczenia, które nie są zwracane przez dostawcę tożsamości, o ile atrybut ten jest ustawiony `DefaultValue` .

Element **OutputClaimsTransformations** może zawierać kolekcję elementów **OutputClaimsTransformation** , które są używane do modyfikowania oświadczeń wyjściowych lub generowania nowych.

W poniższym przykładzie przedstawiono oświadczenia zwrócone przez dostawcę tożsamości konta Microsoft:

- Zastrzeżenie **podrzędne** , które jest mapowane na **issuerUserId** .
- **Nazwa** , która jest mapowana do żądania **DisplayName** .
- **Adres e-mail** bez mapowania nazwy.

Profil techniczny zwraca również oświadczenia, które nie są zwracane przez dostawcę tożsamości:

- **IdentityProvider** , który zawiera nazwę dostawcy tożsamości.
- **AuthenticationSource** z wartością domyślną **socialIdpAuthentication**.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
</OutputClaims>
```

## <a name="metadata"></a>Metadane

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| client_id | Tak | Identyfikator aplikacji dostawcy tożsamości. |
| IdTokenAudience | Nie | Odbiorcy id_token. Jeśli ta wartość jest określona, Azure AD B2C sprawdza, czy `aud` w tokenie zwróconym przez dostawcę tożsamości jest równa określonemu w metadanych IdTokenAudience.  |
| METADANE | Tak | Adres URL wskazujący dokument konfiguracji dostawcy tożsamości OpenID Connect Connect, który jest również znany jako OpenID Connect dobrze znana konfiguracja. Adres URL może zawierać `{tenant}` wyrażenie, które jest zastępowane nazwą dzierżawy.  |
| authorization_endpoint | Nie | Adres URL wskazujący punkt końcowy autoryzacji konfiguracji dostawcy tożsamości OpenID Connect Connect. Wartość metadanych authorization_endpoint ma pierwszeństwo przed `authorization_endpoint` określonym w OpenID connectnym punkcie końcowym konfiguracji. Adres URL może zawierać `{tenant}` wyrażenie, które jest zastępowane nazwą dzierżawy. |
| end_session_endpoint | Nie | Adres URL punktu końcowego sesji końcowej. Wartość metadanych authorization_endpoint ma pierwszeństwo przed `end_session_endpoint` określonym w OpenID connectnym punkcie końcowym konfiguracji. |
| issuer | Nie | Unikatowy identyfikator dostawcy tożsamości OpenID Connect Connect. Wartość metadanych wystawcy ma pierwszeństwo przed `issuer` określonym w OpenID connectnym punkcie końcowym konfiguracji.  Jeśli ta wartość jest określona, Azure AD B2C sprawdza, czy `iss` w tokenie zwróconym przez dostawcę tożsamości jest równa określonemu w metadanych wystawcy. |
| ProviderName | Nie | Nazwa dostawcy tożsamości.  |
| response_types | Nie | Typ odpowiedzi zgodnie z specyfikacją OpenID Connect Connect Core 1,0. Możliwe wartości: `id_token` , `code` , lub `token` . |
| response_mode | Nie | Metoda wykorzystywana przez dostawcę tożsamości do wysyłania wyniku z powrotem do Azure AD B2C. Możliwe wartości: `query` , `form_post` (wartość domyślna), lub `fragment` . |
| scope | Nie | Zakres żądania, który jest zdefiniowany zgodnie z specyfikacją OpenID Connect Connect Core 1,0. Takie jak `openid` , `profile` i `email` . |
| HttpBinding | Nie | Oczekiwano powiązania HTTP z punktami końcowymi tokenu dostępu i tokenów oświadczeń. Możliwe wartości: `GET` lub `POST` .  |
| ValidTokenIssuerPrefixes | Nie | Klucz, którego można użyć do zalogowania się do poszczególnych dzierżawców w przypadku korzystania z dostawcy tożsamości z wieloma dzierżawcami, takiego jak Azure Active Directory. |
| UsePolicyInRedirectUri | Nie | Wskazuje, czy należy używać zasad podczas konstruowania identyfikatora URI przekierowania. Podczas konfigurowania aplikacji w dostawcy tożsamości należy określić identyfikator URI przekierowania. Identyfikator URI przekierowania wskazuje Azure AD B2C, `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp` .  W przypadku określenia `true` tego elementu należy dodać identyfikator URI przekierowania dla każdej używanej zasady. Na przykład: `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/{policy-name}/oauth2/authresp`. |
| MarkAsFailureOnStatusCode5xx | Nie | Wskazuje, czy żądanie do usługi zewnętrznej powinno być oznaczone jako błąd, jeśli kod stanu HTTP znajduje się w zakresie 5xx. Wartość domyślna to `false`. |
| DiscoverMetadataByTokenIssuer | Nie | Wskazuje, czy metadane OIDC powinny być odnajdywane przy użyciu wystawcy w tokenie JWT. |
| IncludeClaimResolvingInClaimsHandling  | Nie | W przypadku oświadczeń wejściowych i wyjściowych określa, czy w profilu technicznym znajduje się [rozpoznawanie oświadczeń](claim-resolver-overview.md) . Możliwe wartości: `true` , lub `false` (wartość domyślna). Jeśli chcesz użyć programu rozpoznawania oświadczeń w profilu technicznym, ustaw dla tej opcji wartość `true` . |
| token_endpoint_auth_method | Nie | Określa sposób, w jaki Azure AD B2C wysyła nagłówek uwierzytelniania do punktu końcowego tokenu. Możliwe wartości: `client_secret_post` (ustawienie domyślne) i `client_secret_basic` (publiczna wersja zapoznawcza). Aby uzyskać więcej informacji, zobacz [sekcję OpenID Connect Connect Client Authentication](https://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication). |
| token_signing_algorithm | Nie | Algorytm podpisywania używany na potrzeby potwierdzeń klientów, gdy metadane **token_endpoint_auth_method** są ustawione na `private_key_jwt` . Możliwe wartości: `RS256` (wartość domyślna). |
| SingleLogoutEnabled | Nie | Wskazuje, czy podczas logowania profil techniczny próbuje wylogować się z federacyjnych dostawców tożsamości. Aby uzyskać więcej informacji, zobacz [Azure AD B2C wylogowania sesji](./session-behavior.md#sign-out).  Możliwe wartości: `true` (ustawienie domyślne) lub `false` . |

```xml
<Metadata>
  <Item Key="ProviderName">https://login.live.com</Item>
  <Item Key="METADATA">https://login.live.com/.well-known/openid-configuration</Item>
  <Item Key="response_types">code</Item>
  <Item Key="response_mode">form_post</Item>
  <Item Key="scope">openid profile email</Item>
  <Item Key="HttpBinding">POST</Item>
  <Item Key="UsePolicyInRedirectUri">false</Item>
  <Item Key="client_id">Your Microsoft application client ID</Item>
</Metadata>
```

### <a name="ui-elements"></a>Elementy interfejsu użytkownika
 
Przy użyciu poniższych ustawień można skonfigurować komunikat o błędzie wyświetlany w przypadku awarii. Metadane należy skonfigurować w profilu technicznym OpenID Connect Connect. Komunikaty o błędach można [lokalizować](localization-string-ids.md#sign-up-or-sign-in-error-messages).

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| UserMessageIfClaimsPrincipalDoesNotExist | Nie | Komunikat, który ma być wyświetlany użytkownikowi w przypadku, gdy w katalogu nie znaleziono konta o podanej nazwie użytkownika. |
| UserMessageIfInvalidPassword | Nie | Komunikat, który ma być wyświetlany użytkownikowi, jeśli hasło jest nieprawidłowe. |
| UserMessageIfOldPasswordUsed| Nie |  Komunikat, który ma być wyświetlany użytkownikowi w przypadku użycia starego hasła.|

## <a name="cryptographic-keys"></a>Klucze kryptograficzne

Element **CryptographicKeys** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| client_secret | Tak | Klucz tajny klienta aplikacji dostawcy tożsamości. Ten klucz kryptograficzny jest wymagany tylko wtedy, gdy **response_types** metadanych ma ustawioną wartość `code` i **token_endpoint_auth_method** jest ustawiona na `client_secret_post` lub `client_secret_basic` . W takim przypadku Azure AD B2C wykonuje inne wywołanie wymiany kodu autoryzacji dla tokenu dostępu. Jeśli metadane są ustawione na wartość można `id_token` pominąć klucz kryptograficzny.  |
| assertion_signing_key | Tak | Klucz prywatny RSA, który będzie używany do podpisywania potwierdzenia klienta. Ten klucz kryptograficzny jest wymagany tylko wtedy, gdy metadane **token_endpoint_auth_method** są ustawione na `private_key_jwt` . |

## <a name="redirect-uri"></a>Identyfikator URI przekierowania

Podczas konfigurowania identyfikatora URI przekierowania dostawcy tożsamości wprowadź wartość `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp` . Pamiętaj, aby zamienić na `{your-tenant-name}` nazwę dzierżawy. Identyfikator URI przekierowania musi zawierać tylko małe litery.

Przykłady:

- [Dodawanie konta Microsoft (MSA) jako dostawcy tożsamości przy użyciu zasad niestandardowych](identity-provider-microsoft-account.md)
- [Logowanie przy użyciu kont usługi Azure AD](identity-provider-azure-ad-single-tenant.md)
- [Zezwalaj użytkownikom na logowanie się do wielodostępnego dostawcy tożsamości usługi Azure AD przy użyciu zasad niestandardowych](identity-provider-azure-ad-multi-tenant.md)