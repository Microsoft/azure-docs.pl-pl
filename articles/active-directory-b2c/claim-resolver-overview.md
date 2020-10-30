---
title: Rozwiązywanie problemów w zasadach niestandardowych
titleSuffix: Azure AD B2C
description: Dowiedz się, jak używać resolverów oświadczeń w zasadach niestandardowych w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/28/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 39b61815c33f933e0cdf08bd46382e74eea2f806
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93040458"
---
# <a name="about-claim-resolvers-in-azure-active-directory-b2c-custom-policies"></a>Informacje o rozpoznawaniu roszczeń w Azure Active Directory B2C zasadach niestandardowych

Mechanizmy rozpoznawania nazw w Azure Active Directory B2C (Azure AD B2C) [zasady niestandardowe](custom-policy-overview.md) zawierają informacje kontekstu dotyczące żądania autoryzacji, takie jak nazwa zasad, identyfikator korelacji żądania, język interfejsu użytkownika i inne.

Aby użyć mechanizmu rozwiązywania konfliktów w ramach oświadczenia wejściowego lub wychodzącego, należy zdefiniować element **ClaimType** typu String w elemencie [ClaimsSchema](claimsschema.md) , a następnie ustawić wartość **DefaultValue** na resolver oświadczenia w elemencie oświadczenia wejściowego lub wychodzącego. Azure AD B2C odczytuje wartość mechanizmu rozwiązywania konfliktów i używa wartości w profilu technicznym.

W poniższym przykładzie typ wystąpienia o nazwie `correlationId` jest zdefiniowany za pomocą typu **danych** `string` .

```xml
<ClaimType Id="correlationId">
  <DisplayName>correlationId</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Request correlation Id</UserHelpText>
</ClaimType>
```

W profilu technicznym zamapuj mechanizm rozwiązywania konfliktów na typ zgłoszenia. Azure AD B2C wypełnia wartość programu rozpoznawania roszczeń `{Context:CorrelationId}` do roszczeń `correlationId` i wysyła je do profilu technicznego.

```xml
<InputClaim ClaimTypeReferenceId="correlationId" DefaultValue="{Context:CorrelationId}" />
```

## <a name="claim-resolver-types"></a>Typy mechanizmu rozwiązywania konfliktów

W poniższych sekcjach znajduje się lista dostępnych elementów rozwiązywania problemów.

### <a name="culture"></a>Kultura

| Claim | Opis | Przykład |
| ----- | ----------- | --------|
| {Culture: LanguageName} | Dwuliterowy kod ISO dla języka. | en |
| {Culture: LCID}   | Identyfikator LCID kodu języka. | 1045 |
| {Culture: RegionName} | Dwuliterowy kod ISO dla regionu. | USA |
| {Culture: RFC5646} | Kod języka RFC5646. | en-US |

### <a name="policy"></a>Zasady

| Claim | Opis | Przykład |
| ----- | ----------- | --------|
| {Policy: PolicyId} | Nazwa zasad jednostki uzależnionej. | B2C_1A_signup_signin |
| {Policy: RelyingPartyTenantId} | Identyfikator dzierżawy zasad jednostki uzależnionej. | your-tenant.onmicrosoft.com |
| {Policy: TenantObjectId} | Identyfikator obiektu dzierżawy zasad jednostki uzależnionej. | 00000000-0000-0000-0000-000000000000 |
| {Policy: TrustFrameworkTenantId} | Identyfikator dzierżawy struktury zaufania. | your-tenant.onmicrosoft.com |

### <a name="openid-connect"></a>OpenID Connect

| Claim | Opis | Przykład |
| ----- | ----------- | --------|
| {OIDC: AuthenticationContextReferences} |`acr_values`Parametr ciągu zapytania. | Nie dotyczy |
| {OIDC: ClientId} |`client_id`Parametr ciągu zapytania. | 00000000-0000-0000-0000-000000000000 |
| {OIDC: DomainHint} |`domain_hint`Parametr ciągu zapytania. | facebook.com |
| {OIDC: LoginHint} |  `login_hint`Parametr ciągu zapytania. | someone@contoso.com |
| {OIDC: MaxAge} | Element `max_age`. | Nie dotyczy |
| {OIDC: nonce} |`Nonce`Parametr ciągu zapytania. | defaultNonce |
| {OIDC: hasło}| [Poświadczenia hasła właściciela zasobu przepływu](ropc-custom.md) hasła użytkownika.| password1| 
| {OIDC: Prompt} | `prompt`Parametr ciągu zapytania. | logowanie |
| {OIDC: RedirectUri} |`redirect_uri`Parametr ciągu zapytania. | https://jwt.ms |
| {OIDC: Resource} |`resource`Parametr ciągu zapytania. | Nie dotyczy |
| {OIDC: Scope} |`scope`Parametr ciągu zapytania. | OpenID Connect |
| {OIDC: username}| Nazwa użytkownika [przepływu poświadczeń hasła właściciela zasobu](ropc-custom.md) .| emily@contoso.com| 

### <a name="context"></a>Kontekst

| Claim | Opis | Przykład |
| ----- | ----------- | --------|
| {Context: BuildNumber} | Wersja struktury obsługi tożsamości (numer kompilacji).  | 1.0.507.0 |
| {Context: identyfikator korelacji} | Identyfikator korelacji.  | 00000000-0000-0000-0000-000000000000 |
| {Context: DateTimeInUtc} |Data i godzina w formacie UTC.  | 10/10/2018 12:00:00 PM |
| {Context: DeploymentMode} |Tryb wdrażania zasad.  | Produkcja |
| {Context: IPAddress} | Adres IP użytkownika. | 11.111.111.11 |
| {Context: KMSI} | Wskazuje, czy pole wyboru nie [wylogowuj mnie](custom-policy-keep-me-signed-in.md) jest zaznaczone. |  true |

### <a name="claims"></a>Oświadczenia 

| Claim | Opis | Przykład |
| ----- | ----------- | --------|
| {Claim: typ zgłoszenia} | Identyfikator typu "Claim" jest już zdefiniowany w sekcji ClaimsSchema w pliku zasad lub nadrzędnym pliku zasad.  Na przykład: `{Claim:displayName}` , lub `{Claim:objectId}` . | Wartość typu zgłoszenia.|


### <a name="oauth2-key-value-parameters"></a>OAuth2 klucz-wartość parametrów

Wszelkie nazwy parametrów dołączone jako część żądania OIDC lub OAuth2 mogą być mapowane do roszczeń w podróży użytkownika. Na przykład żądanie z aplikacji może zawierać parametr ciągu zapytania z nazwą `app_session` , `loyalty_number` lub dowolnym niestandardowym ciągiem zapytania.

| Claim | Opis | Przykład |
| ----- | ----------------------- | --------|
| {OAUTH-KV: campaignId} | Parametr ciągu zapytania. | Hawaje |
| {OAUTH-KV: app_session} | Parametr ciągu zapytania. | A3C5R |
| {OAUTH-KV: loyalty_number} | Parametr ciągu zapytania. | 1234 |
| {OAUTH-KV: dowolny niestandardowy ciąg zapytania} | Parametr ciągu zapytania. | Nie dotyczy |

### <a name="oauth2"></a>OAuth2

| Claim | Opis | Przykład |
| ----- | ----------------------- | --------|
| {OAuth2: access_token} | Token dostępu. | Nie dotyczy |
| {OAuth2: refresh_token} | Token odświeżania. | Nie dotyczy |


### <a name="saml"></a>SAML

| Claim | Opis | Przykład |
| ----- | ----------- | --------|
| {SAML: AuthnContextClassReferences} | `AuthnContextClassRef`Wartość elementu z żądania SAML. | urn: języka Oasis: names: TC: SAML: 2.0: AC: klasy: PasswordProtectedTransport |
| {SAML: NameIdPolicyFormat} | `Format`Atrybut, od `NameIDPolicy` elementu żądania SAML. | urn: języka Oasis: names: TC: SAML: 1.1: NameID-format: emailAddress |
| {SAML: Issuer} |  `Issuer`Wartość elementu SAML żądania SAML.| `https://contoso.com` |
| {SAML: AllowCreate} | `AllowCreate`Wartość atrybutu z `NameIDPolicy` elementu żądania języka SAML. | Prawda |
| {SAML: ForceAuthn} | `ForceAuthN`Wartość atrybutu z `AuthnRequest` elementu żądania języka SAML. | Prawda |
| {SAML: ProviderName} | `ProviderName`Wartość atrybutu z `AuthnRequest` elementu żądania języka SAML.| Contoso.com |
| {SAML: RelayState} | `RelayState`Parametr ciągu zapytania.| 
| {SAML: subject} | `Subject`Z elementu NameID żądania SAML AuthN.| 

## <a name="using-claim-resolvers"></a>Korzystanie z resolverów roszczeń

Można użyć resolverów oświadczeń z następującymi elementami:

| Element | Element | Ustawienia |
| ----- | ----------------------- | --------|
|Profil techniczny Application Insights |`InputClaim` | |
|Profil techniczny [Azure Active Directory](active-directory-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|Profil techniczny [OAuth2](oauth2-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|Profil techniczny [OpenID Connect Connect](openid-connect-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|Profil techniczny [transformacji oświadczeń](claims-transformation-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|Profil techniczny [dostawcy RESTful](restful-technical-profile.md)| `InputClaim`| 1, 2|
|Profil techniczny [dostawcy tożsamości SAML](saml-identity-provider-technical-profile.md)| `OutputClaim`| 1, 2|
|Profil techniczny [z własnym potwierdzeniem](self-asserted-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|[ContentDefinition](contentdefinitions.md)| `LoadUri`| |
|[ContentDefinitionParameters](relyingparty.md#contentdefinitionparameters)| `Parameter` | |
|Profil techniczny [RelyingParty](relyingparty.md#technicalprofile)| `OutputClaim`| 2 |

Ustawienia:
1. `IncludeClaimResolvingInClaimsHandling`Metadane muszą być ustawione na wartość `true` .
1. Atrybut oświadczeń wejściowych lub wyjściowych `AlwaysUseDefaultValue` musi być ustawiony na wartość `true` .

## <a name="claim-resolvers-samples"></a>Przykłady elementów rozpoznawania nazw

### <a name="restful-technical-profile"></a>Profil techniczny RESTful

W profilu technicznym [RESTful](restful-technical-profile.md) może być konieczne wysłanie języka użytkownika, nazwy zasad, zakresu i identyfikatora klienta. W oparciu o oświadczenia, interfejs API REST może uruchamiać niestandardową logikę biznesową i w razie potrzeby zgłaszać zlokalizowany komunikat o błędzie.

W poniższym przykładzie przedstawiono profil techniczny RESTful w tym scenariuszu:

```xml
<TechnicalProfile Id="REST">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="policyName" DefaultValue="{Policy:PolicyId}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="{OIDC:Scope}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="clientId" DefaultValue="{OIDC:ClientId}" AlwaysUseDefaultValue="true" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="direct-sign-in"></a>Bezpośrednie logowanie

Korzystając z resolverów roszczeń, można wstępnie wypełnić nazwę logowania lub bezpośrednio zalogować do określonego dostawcy tożsamości społecznościowej, takiego jak Facebook, LinkedIn lub konto Microsoft. Aby uzyskać więcej informacji, zobacz [Konfigurowanie bezpośredniego logowania przy użyciu Azure Active Directory B2C](direct-signin.md).

### <a name="dynamic-ui-customization"></a>Dynamiczne dostosowywanie interfejsu użytkownika

Azure AD B2C umożliwia przekazywanie parametrów ciągu zapytania do punktów końcowych definicji zawartości HTML w celu dynamicznego renderowania zawartości strony. Na przykład ta funkcja umożliwia modyfikowanie obrazu tła na stronie rejestracji na Azure AD B2C lub logowania na podstawie parametru niestandardowego, który jest przekazywany z aplikacji sieci Web lub mobilnej. Aby uzyskać więcej informacji, zobacz [dynamiczne Konfigurowanie interfejsu użytkownika przy użyciu zasad niestandardowych w programie Azure Active Directory B2C](custom-policy-ui-customization.md#configure-dynamic-custom-page-content-uri). Możesz również lokalizować stronę HTML na podstawie parametru języka lub można zmienić zawartość na podstawie identyfikatora klienta.

Poniższy przykład przekazuje parametr ciągu zapytania o nazwie **campaignId** wartością `Hawaii` , kod **języka** `en-US` i **aplikację** reprezentującą identyfikator klienta:

```xml
<UserJourneyBehaviors>
  <ContentDefinitionParameters>
    <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
    <Parameter Name="language">{Culture:RFC5646}</Parameter>
    <Parameter Name="app">{OIDC:ClientId}</Parameter>
  </ContentDefinitionParameters>
</UserJourneyBehaviors>
```

W związku z tym Azure AD B2C wysyła powyższe parametry do strony zawartości HTML:

```
/selfAsserted.aspx?campaignId=hawaii&language=en-US&app=0239a9cc-309c-4d41-87f1-31288feb2e82
```

### <a name="content-definition"></a>Definicja zawartości

W [ContentDefinition](contentdefinitions.md) `LoadUri` można wysyłać resolvery roszczeń w celu ściągania zawartości z różnych miejsc, na podstawie używanych parametrów.

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

### <a name="application-insights-technical-profile"></a>Profil techniczny Application Insights

Za pomocą usługi Azure Application Insights i rozwiązania do rozwiązywania problemów możesz uzyskiwać wgląd w zachowania użytkowników. W profilu technicznym Application Insights wysyłane są oświadczenia wejściowe, które są utrwalane w usłudze Azure Application Insights. Aby uzyskać więcej informacji, zobacz [śledzenie zachowania użytkowników w Azure AD B2C podróże przy użyciu Application Insights](analytics-with-application-insights.md). Poniższy przykład wysyła identyfikator zasad, identyfikator korelacji, język i identyfikator klienta do usługi Azure Application Insights.

```xml
<TechnicalProfile Id="AzureInsights-Common">
  <DisplayName>Alternate Email</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
    <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:CorrelationId}" DefaultValue="{Context:CorrelationId}" />
    <InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
    <InputClaim ClaimTypeReferenceId="AppId" PartnerClaimType="{property:App}" DefaultValue="{OIDC:ClientId}" />
  </InputClaims>
</TechnicalProfile>
```

### <a name="relying-party-policy"></a>Zasady jednostki uzależnionej

W profilu technicznym zasad [jednostki uzależnionej](relyingparty.md) możesz chcieć wysłać identyfikator dzierżawy lub identyfikator korelacji do aplikacji jednostki uzależnionej w ramach tokenu JWT.

```xml
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
        <OutputClaim ClaimTypeReferenceId="correlationId" AlwaysUseDefaultValue="true" DefaultValue="{Context:CorrelationId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
```
