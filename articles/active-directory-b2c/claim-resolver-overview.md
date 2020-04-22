---
title: Rozpoznawanie oświadczeń w zasadach niestandardowych
titleSuffix: Azure AD B2C
description: Dowiedz się, jak używać programów rozpoznawania oświadczeń w zasadach niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/21/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0bdede482b79c82e6e05b1429cb7c17399bc2277
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81756610"
---
# <a name="about-claim-resolvers-in-azure-active-directory-b2c-custom-policies"></a>Rozpoznawanie oświadczeń w zasadach niestandardowych usługi Azure Active Directory B2C — informacje

Rozpoznawanie oświadczeń w [zasadach niestandardowych](custom-policy-overview.md) usługi Azure Active Directory B2C (Azure AD B2C) zawierają informacje kontekstowe dotyczące żądania autoryzacji, takie jak nazwa zasad, identyfikator korelacji żądania, język interfejsu użytkownika i inne.

Aby użyć rozpoznawania nazw w żądaniu danych wejściowych lub wyjściowych, należy zdefiniować ciąg **ClaimType**, w ramach [ClaimsSchema](claimsschema.md) element, a następnie ustawić **DefaultValue** do rozpoznawania oświadczeń w wejściu lub wynikowym elementem oświadczenia. Usługa Azure AD B2C odczytuje wartość rozpoznawania oświadczeń i używa wartości w profilu technicznym.

W poniższym przykładzie typ `correlationId` oświadczenia o nazwie `string`jest zdefiniowany za pomocą **datatype** .

```XML
<ClaimType Id="correlationId">
  <DisplayName>correlationId</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Request correlation Id</UserHelpText>
</ClaimType>
```

W profilu technicznym mapuj program rozpoznawania oświadczeń na typ oświadczenia. Usługa Azure AD B2C wypełnia wartość rozpoznawania `{Context:CorrelationId}` oświadczeń `correlationId` do oświadczenia i wysyła oświadczenie do profilu technicznego.

```XML
<InputClaim ClaimTypeReferenceId="correlationId" DefaultValue="{Context:CorrelationId}" />
```

## <a name="claim-resolver-types"></a>Typy rozpoznawania oświadczeń

Poniższe sekcje listy dostępnych rozpoznawania oświadczeń.

### <a name="culture"></a>Culture

| Claim | Opis | Przykład |
| ----- | ----------- | --------|
| {Kultura:Nazwa języka} | Dwuliterowy kod ISO dla języka. | pl |
| {Kultura:LCID}   | LCID kodu języka. | 1045 |
| {Kultura:Nazwa regionu} | Dwuliterowy kod ISO dla regionu. | USA |
| {Kultura:RFC5646} | Kod języka RFC5646. | pl-PL |

### <a name="policy"></a>Zasady

| Claim | Opis | Przykład |
| ----- | ----------- | --------|
| {Zasady:PolicyId} | Nazwa zasad jednostki uzależniającej. | B2C_1A_signup_signin |
| {Zasady:RelyingPartyTenantId} | Identyfikator dzierżawy zasad jednostki uzależniającej. | your-tenant.onmicrosoft.com |
| {Zasada:Identyfikatorobiekta dzierżawy} | Identyfikator obiektu dzierżawcy zasad jednostki uzależniającej. | 00000000-0000-0000-0000-000000000000 |
| {Zasada:TrustFrameworkTenantId} | Identyfikator dzierżawy struktury zaufania. | your-tenant.onmicrosoft.com |

### <a name="openid-connect"></a>OpenID Connect

| Claim | Opis | Przykład |
| ----- | ----------- | --------|
| {OIDC:AuthenticationContextReferences} |Parametr `acr_values` ciągu kwerendy. | Nie dotyczy |
| {OIDC:Identyfikator klienta} |Parametr `client_id` ciągu kwerendy. | 00000000-0000-0000-0000-000000000000 |
| {OIDC:DomainHint} |Parametr `domain_hint` ciągu kwerendy. | facebook.com |
| {OIDC:LoginHint} |  Parametr `login_hint` ciągu kwerendy. | someone@contoso.com |
| {OIDC:MaxAge} | Element `max_age`. | Nie dotyczy |
| {OIDC:Nonce} |Parametr `Nonce` ciągu kwerendy. | defaultNonce |
| {OIDC:Hasło}| Hasło [właściciela zasobu przesyła](ropc-custom.md) hasło użytkownika.| hasło1| 
| {OIDC:Monit} | Parametr `prompt` ciągu kwerendy. | logowanie |
| {OIDC:RedirectUri} |Parametr `redirect_uri` ciągu kwerendy. | https://jwt.ms |
| {OIDC:Zasób} |Parametr `resource` ciągu kwerendy. | Nie dotyczy |
| {OIDC:Zakres} |Parametr `scope` ciągu kwerendy. | Openid |
| {OIDC:Nazwa użytkownika}| Poświadczenia [hasła właściciela zasobu przepływają](ropc-custom.md) nazwę użytkownika.| emily@contoso.com| 

### <a name="context"></a>Kontekst

| Claim | Opis | Przykład |
| ----- | ----------- | --------|
| {Kontekst:Numer kompilacji} | Wersja struktury środowiska tożsamości (numer kompilacji).  | 1.0.507.0 |
| {Kontekst:Ida korelacji} | Identyfikator korelacji.  | 00000000-0000-0000-0000-000000000000 |
| {Kontekst:DateTimeInUtc} |Data daty w czasie UTC.  | 10.10.2018 12:00:00 |
| {Kontekst:Tryb wdrażania} |Tryb wdrażania zasad.  | Produkcja |
| {Kontekst:adres IPAddress} | Adres IP użytkownika. | 11.111.111.11 |
| {Kontekst:KMSI} | Wskazuje, czy pole [wyboru Niech mnie zalogowano.](custom-policy-keep-me-signed-in.md) |  true |

### <a name="claims"></a>Oświadczenia 

| Claim | Opis | Przykład |
| ----- | ----------- | --------|
| {Oświadczenie:typ oświadczenia} | Identyfikator typu oświadczenia zdefiniowanego już w sekcji ClaimsSchema w pliku zasad lub nadrzędnym pliku zasad.  Na przykład: `{Claim:displayName}` `{Claim:objectId}`, lub . | Wartość typu oświadczenia.|


### <a name="oauth2-key-value-parameters"></a>Parametry wartości klucza OAuth2

Dowolna nazwa parametru zawarta jako część żądania OIDC lub OAuth2 może być mapowana na oświadczenie w podróży użytkownika. Na przykład żądanie z aplikacji może zawierać parametr ciągu `app_session`zapytania `loyalty_number`o nazwie , lub dowolny niestandardowy ciąg zapytania.

| Claim | Opis | Przykład |
| ----- | ----------------------- | --------|
| {OAUTH-KV:campaignId} | Parametr ciągu kwerendy. | Hawaje |
| {OAUTH-KV:app_session} | Parametr ciągu kwerendy. | A3C5R ( A3C5R ) |
| {OAUTH-KV:loyalty_number} | Parametr ciągu kwerendy. | 1234 |
| {OAUTH-KV:dowolny niestandardowy ciąg zapytania} | Parametr ciągu kwerendy. | Nie dotyczy |

### <a name="oauth2"></a>OAuth2

| Claim | Opis | Przykład |
| ----- | ----------------------- | --------|
| {oauth2:access_token} | Token dostępu. | Nie dotyczy |


### <a name="saml"></a>SAML

| Claim | Opis | Przykład |
| ----- | ----------- | --------|
| {SAML:AuthnContextClassReferences} | Wartość `AuthnContextClassRef` elementu, z żądania SAML. | urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport |
| {SAML:NameIdPolicyFormat} | Atrybut, `Format` z `NameIDPolicy` elementu żądania SAML. | urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress |
| {SAML:Wystawca} |  Wartość elementu `Issuer` SAML żądania SAML.| `https://contoso.com` |
| {SAML:AllowCreate} | Wartość `AllowCreate` atrybutu, `NameIDPolicy` z elementu żądania SAML. | True |
| {SAML:ForceAuthn} | Wartość `ForceAuthN` atrybutu, `AuthnRequest` z elementu żądania SAML. | True |
| {SAML:Nazwa dostawcy} | Wartość `ProviderName` atrybutu, `AuthnRequest` z elementu żądania SAML.| Contoso.com |
| {SAML:Stan przekaźnika} | Parametr `RelayState` ciągu kwerendy.| 

## <a name="using-claim-resolvers"></a>Korzystanie z programów rozpoznawania oświadczeń

Można użyć programów rozpoznawania oświadczeń z następującymi elementami:

| Element | Element | Ustawienia |
| ----- | ----------------------- | --------|
|Profil techniczny usługi Application Insights |`InputClaim` | |
|Profil techniczny [usługi Azure Active Directory](active-directory-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|Profil techniczny [OAuth2](oauth2-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|Profil techniczny [OpenID Connect](openid-connect-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|Profil techniczny [transformacji oświadczeń](claims-transformation-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|Profil techniczny [dostawcy RESTful](restful-technical-profile.md)| `InputClaim`| 1, 2|
|Profil techniczny [SAML2](saml-technical-profile.md)| `OutputClaim`| 1, 2|
|[Samodzielnie potwierdzony](self-asserted-technical-profile.md) profil techniczny| `InputClaim`, `OutputClaim`| 1, 2|
|[Definicja zawartości](contentdefinitions.md)| `LoadUri`| |
|[ContentDefinitionParameters (Parametry definicji zawartości)](relyingparty.md#contentdefinitionparameters)| `Parameter` | |
|Profil techniczny [RelyingParty](relyingparty.md#technicalprofile)| `OutputClaim`| 2 |

Ustawienia:
1. Metadane `IncludeClaimResolvingInClaimsHandling` muszą być `true`ustawione na .
1. Atrybut oświadczeń wejściowych `AlwaysUseDefaultValue` lub wyjściowych `true`musi być ustawiony na .

## <a name="claim-resolvers-samples"></a>Próbki rozpoznawania nazw

### <a name="restful-technical-profile"></a>Restful profil techniczny

W profilu technicznym [RESTful](restful-technical-profile.md) można wysłać język użytkownika, nazwę zasad, zakres i identyfikator klienta. Na podstawie oświadczeń interfejs API REST można uruchomić niestandardową logikę biznesową i w razie potrzeby podnieść zlokalizowany komunikat o błędzie.

W poniższym przykładzie pokazano profil techniczny RESTful w tym scenariuszu:

```XML
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

Korzystając z programów rozpoznawania oświadczeń, można wstępnie wypełnić nazwę logowania lub bezpośrednie zalogowanie się do określonego dostawcy tożsamości społecznościowych, takiego jak Facebook, LinkedIn lub konto Microsoft. Aby uzyskać więcej informacji, zobacz [Konfigurowanie bezpośredniego logowania przy użyciu usługi Azure Active Directory B2C](direct-signin.md).

### <a name="dynamic-ui-customization"></a>Dostosowywanie dynamicznego interfejsu użytkownika

Usługa Azure AD B2C umożliwia przekazywanie parametrów ciągu zapytania do punktów końcowych definicji zawartości HTML, aby dynamicznie renderować zawartość strony. Na przykład ta funkcja umożliwia modyfikowanie obrazu tła na stronie rejestracji lub logowania usługi Azure AD B2C na podstawie niestandardowego parametru, który jest przekazywał z aplikacji sieci web lub aplikacji mobilnej. Aby uzyskać więcej informacji, zobacz [Dynamicznie konfigurować interfejs użytkownika przy użyciu zasad niestandardowych w usłudze Azure Active Directory B2C](custom-policy-ui-customization.md#configure-dynamic-custom-page-content-uri). Można również zlokalizować stronę HTML na podstawie parametru języka lub zmienić zawartość na podstawie identyfikatora klienta.

Poniższy przykład przekazuje w parametrze ciągu zapytania `Hawaii`o nazwie **campaignId** o wartości , kod **języka** `en-US`, i **aplikacji** reprezentującej identyfikator klienta:

```XML
<UserJourneyBehaviors>
  <ContentDefinitionParameters>
    <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
    <Parameter Name="language">{Culture:RFC5646}</Parameter>
    <Parameter Name="app">{OIDC:ClientId}</Parameter>
  </ContentDefinitionParameters>
</UserJourneyBehaviors>
```

W rezultacie usługa Azure AD B2C wysyła powyższe parametry do strony zawartości HTML:

```
/selfAsserted.aspx?campaignId=hawaii&language=en-US&app=0239a9cc-309c-4d41-87f1-31288feb2e82
```

### <a name="content-definition"></a>Definicja zawartości

W [ContentDefinition](contentdefinitions.md) `LoadUri`można wysyłać programy rozpoznawania oświadczeń do ściągania zawartości z różnych miejsc na podstawie użytych parametrów.

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

### <a name="application-insights-technical-profile"></a>Profil techniczny usługi Application Insights

Dzięki usłudze Azure Application Insights i programom rozpoznawania oświadczeń można uzyskać szczegółowe informacje na temat zachowania użytkowników. W profilu technicznym usługi Application Insights wysyłasz oświadczenia wejściowe, które są utrwalone w usłudze Azure Application Insights. Aby uzyskać więcej informacji, zobacz [Śledzenie zachowania użytkowników w przypadku podróży usługi Azure AD B2C przy użyciu usługi Application Insights](analytics-with-application-insights.md). Poniższy przykład wysyła identyfikator zasad, identyfikator korelacji, język i identyfikator klienta do usługi Azure Application Insights.

```XML
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

### <a name="relying-party-policy"></a>Zasady jednostki uzależniającej

W profilu technicznym zasad [jednostki uzależniona](relyingparty.md) można wysłać identyfikator dzierżawy lub identyfikator korelacji do aplikacji jednostki uzależniona w ramach JWT.

```XML
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
