---
title: RelyingParty — Azure Active Directory B2C | Microsoft Docs
description: Określ element RelyingParty zasad niestandardowych w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0f0f3b6ffcb7ee12a692470b922cf23a3f0f40f0
ms.sourcegitcommit: 89c0482c16bfec316a79caa3667c256ee40b163f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "97858438"
---
# <a name="relyingparty"></a>RelyingParty

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Element **RelyingParty** określa podróż użytkownika, aby wymusić bieżące żądanie do Azure Active Directory B2C (Azure AD B2C). Określa także listę oświadczeń, które są wymagane przez aplikację jednostki uzależnionej (RP) jako część wystawionego tokenu. Aplikacja RP, taka jak aplikacja sieci Web, aplikacji mobilnej lub klasycznej, wywołuje plik zasad RP. Plik zasad RP wykonuje określone zadanie, takie jak logowanie, Resetowanie hasła lub edytowanie profilu. Wiele aplikacji może korzystać z tych samych zasad RP, a pojedyncza aplikacja może korzystać z wielu zasad. Wszystkie aplikacje RP otrzymują ten sam token z oświadczeniami, a użytkownik przechodzi przez tę samą podróż użytkownika.

Poniższy przykład przedstawia element **RelyingParty** w pliku zasad *B2C_1A_signup_signin* :

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="your-tenant.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin"
  PublicPolicyUri="http://your-tenant.onmicrosoft.com/B2C_1A_signup_signin">

  <BasePolicy>
    <TenantId>your-tenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>

  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="7"/>
      <SessionExpiryType>Rolling</SessionExpiryType>
      <SessionExpiryInSeconds>300</SessionExpiryInSeconds>
      <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="your-application-insights-key" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
      </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Description>The policy profile</Description>
      <Protocol Name="OpenIdConnect" />
      <Metadata>collection of key/value pairs of data</Metadata>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
  ...
```

Opcjonalny element **RelyingParty** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| DefaultUserJourney | 1:1 | Domyślna podróż użytkownika dla aplikacji RP. |
| Punkty końcowe | 0:1 | Lista punktów końcowych. Aby uzyskać więcej informacji, zobacz [punkt końcowy UserInfo](userinfo-endpoint.md). |
| UserJourneyBehaviors | 0:1 | Zakres zachowań podróży użytkownika. |
| Profilu technicznym | 1:1 | Profil techniczny obsługiwany przez aplikację RP. Profil techniczny zawiera kontrakt dla aplikacji RP do kontaktu Azure AD B2C. |

## <a name="endpoints"></a>Punkty końcowe

Element **Endpoints** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Punkt końcowy | 1:1 | Odwołanie do punktu końcowego.|

Element **Endpoint** zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Id | Tak | Unikatowy identyfikator punktu końcowego.|
| UserJourneyReferenceId | Tak | Identyfikator podróży użytkownika w zasadach. Aby uzyskać więcej informacji, zobacz [podróże użytkownika](userjourneys.md)  | 

W poniższym przykładzie przedstawiono jednostkę uzależnioną z [punktem końcowym UserInfo](userinfo-endpoint.md):

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <Endpoints>
    <Endpoint Id="UserInfo" UserJourneyReferenceId="UserInfoJourney" />
  </Endpoints>
  ...
```

## <a name="defaultuserjourney"></a>DefaultUserJourney

`DefaultUserJourney`Element określa odwołanie do identyfikatora podróży użytkownika, która jest zwykle zdefiniowana w zasadach podstawowych lub zasad rozszerzenia. W poniższych przykładach przedstawiono drogę rejestracji lub logowania użytkownika określoną w elemencie **RelyingParty** :

Zasady *B2C_1A_signup_signin* :

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

*B2C_1A_TrustFrameWorkBase* lub *B2C_1A_TrustFrameworkExtensionPolicy*:

```xml
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
  ...
```

Element **DefaultUserJourney** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Identyfikator odwołania | Tak | Identyfikator podróży użytkownika w zasadach. Aby uzyskać więcej informacji, zobacz [podróże użytkownika](userjourneys.md) |

## <a name="userjourneybehaviors"></a>UserJourneyBehaviors

Element **UserJourneyBehaviors** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| SingleSignOn | 0:1 | Zakres działania dotyczącego logowania jednokrotnego (SSO) w trakcie podróży użytkownika. |
| SessionExpiryType |0:1 | Zachowanie podczas uwierzytelniania sesji. Możliwe wartości: `Rolling` lub `Absolute` . `Rolling`Wartość (domyślnie) wskazuje, że użytkownik pozostanie zalogowany, o ile użytkownik jest stale aktywny w aplikacji. `Absolute`Wartość oznacza, że użytkownik jest zmuszony do ponownego uwierzytelnienia po upływie czasu określonego przez okres istnienia sesji aplikacji. |
| SessionExpiryInSeconds | 0:1 | Okres istnienia pliku cookie sesji B2C's usługi Azure AD został określony jako liczba całkowita przechowywana w przeglądarce użytkownika po pomyślnym uwierzytelnieniu. |
| JourneyInsights | 0:1 | Klucz Instrumentacji usługi Azure Application Insights, który ma być używany. |
| ContentDefinitionParameters | 0:1 | Lista par wartości klucza do dołączenia do identyfikatora URI ładowania definicji zawartości. |
|ScriptExecution| 0:1| Obsługiwane tryby wykonywania [języka JavaScript](javascript-and-page-layout.md) . Możliwe wartości: `Allow` lub `Disallow` (wartość domyślna).

### <a name="singlesignon"></a>SingleSignOn

Element **SingleSignon** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Zakres | Tak | Zakres zachowania logowania jednokrotnego. Możliwe wartości: `Suppressed` , `Tenant` , `Application` lub `Policy` . `Suppressed`Wartość wskazuje, że zachowanie jest pomijane, a użytkownik jest zawsze monitowany o wybranie dostawcy tożsamości.  `Tenant`Wartość wskazuje, że zachowanie jest stosowane do wszystkich zasad w dzierżawie. Na przykład użytkownik przechodzenia przez dwie przejazdy zasad dla dzierżawy nie jest monitowany o wybranie dostawcy tożsamości. `Application`Wartość wskazuje, że zachowanie jest stosowane do wszystkich zasad dla aplikacji zgłaszającej żądanie. Na przykład użytkownik przechodzenia przez dwie przejazdy zasad dla aplikacji nie jest monitowany o wybranie dostawcy tożsamości. `Policy`Wartość wskazuje, że zachowanie dotyczy tylko zasad. Na przykład użytkownik przechodzący przez dwie przedziały zasad dla struktury zaufania jest monitowany o wybranie dostawcy tożsamości podczas przełączania między zasadami. |
| KeepAliveInDays | Tak | Określa, jak długo użytkownik jest zalogowany. Ustawienie wartości 0 powoduje wyłączenie funkcji KMSI. Aby uzyskać więcej informacji, zobacz temat nie [wylogowuj mnie](session-behavior.md?pivots=b2c-custom-policy#enable-keep-me-signed-in-kmsi). |
|EnforceIdTokenHintOnLogout| Nie|  Wymuś przekazania wcześniej wystawionego tokenu ID do punktu końcowego wylogowania jako wskazówkę dotyczącą bieżącej uwierzytelnionej sesji użytkownika końcowego z klientem. Możliwe wartości: `false` (ustawienie domyślne) lub `true` . Aby uzyskać więcej informacji, zobacz [Logowanie w sieci Web za pomocą usługi OpenID Connect Connect](openid-connect.md).  |


## <a name="journeyinsights"></a>JourneyInsights

Element **JourneyInsights** zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| TelemetryEngine | Tak | Wartość musi być `ApplicationInsights` . |
| InstrumentationKey | Tak | Ciąg, który zawiera klucz Instrumentacji dla elementu Application Insights. |
| Deweloper | Tak | Możliwe wartości: `true` lub `false` . Jeśli `true` Application Insights przyspiesza dane telemetryczne za pomocą potoku przetwarzania. To ustawienie jest przydatne w przypadku programowania, ale jest ograniczone do dużych woluminów. Szczegółowe dzienniki działań są przeznaczone tylko do ułatwienia tworzenia zasad niestandardowych. Nie używaj trybu deweloperskiego w środowisku produkcyjnym. Dzienniki zbierają wszystkie oświadczenia wysyłane do i od dostawców tożsamości podczas opracowywania. Jeśli jest używany w środowisku produkcyjnym, programista przyjmuje odpowiedzialność za dane OSOBowe (informacje z możliwością zidentyfikowaną prywatnie) zebrane w dzienniku usługi App Insights. Te szczegółowe dzienniki są zbierane tylko wtedy, gdy ta wartość jest ustawiona na `true` .|
| ClientEnabled | Tak | Możliwe wartości: `true` lub `false` . Jeśli `true` program wysyła skrypt po stronie klienta Application Insights na potrzeby śledzenia widoku strony i błędów po stronie klienta. |
| ServerEnabled | Tak | Możliwe wartości: `true` lub `false` . Jeśli `true` , program wysyła istniejący kod JSON UserJourneyRecorder jako zdarzenie niestandardowe do Application Insights. |
| TelemetryVersion | Tak | Wartość musi być `1.0.0` . |

Aby uzyskać więcej informacji, zobacz [zbieranie dzienników](troubleshoot-with-application-insights.md)

## <a name="contentdefinitionparameters"></a>ContentDefinitionParameters

Korzystając z zasad niestandardowych w Azure AD B2C, można wysłać parametr w ciągu zapytania. Przekazanie parametru do punktu końcowego HTML pozwala na dynamiczną zmianę zawartość strony. Na podstawie parametru przekazywanego z aplikacji internetowej lub aplikacji mobilnej można na przykład zmienić obraz tła na stronie rejestracji lub logowania usługi Azure AD B2C. Azure AD B2C przekazuje parametry ciągu zapytania do dynamicznego pliku HTML, takiego jak plik aspx.

Poniższy przykład przekazuje parametr o nazwie `campaignId` przy użyciu wartości `hawaii` w ciągu zapytania:

`https://login.microsoft.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?pB2C_1A_signup_signin&client_id=a415078a-0402-4ce3-a9c6-ec1947fcfb3f&nonce=defaultNonce&redirect_uri=http%3A%2F%2Fjwt.io%2F&scope=openid&response_type=id_token&prompt=login&campaignId=hawaii`

Element **ContentDefinitionParameters** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| ContentDefinitionParameter | 0: n | Ciąg zawierający parę wartości klucza, która jest dołączana do ciągu zapytania w identyfikatorze URI ładowania definicji zawartości. |

Element **ContentDefinitionParameter** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Nazwa | Tak | Nazwa pary klucz wartość. |

Aby uzyskać więcej informacji, zobacz [Konfigurowanie interfejsu użytkownika z zawartością dynamiczną przy użyciu zasad niestandardowych](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri)

## <a name="technicalprofile"></a>Profilu technicznym

Element **profilu technicznym** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Id | Tak | Wartość musi być `PolicyProfile` . |

**Profilu technicznym** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Nazwa wyświetlana | 1:1 | Ciąg, który zawiera nazwę profilu technicznego. |
| Opis | 0:1 | Ciąg, który zawiera opis profilu technicznego. |
| Protokół | 1:1 | Protokół używany w Federacji. |
| Metadane | 0:1 | Kolekcja *elementów* par klucz/wartość wykorzystywana przez protokół do komunikowania się z punktem końcowym w trakcie transakcji w celu skonfigurowania interakcji między jednostką uzależnioną i innymi uczestnikami społeczności. |
| OutputClaims | 1:1 | Lista typów roszczeń, które są pobierane jako dane wyjściowe w profilu technicznym. Każdy z tych elementów zawiera odwołanie do elementu **ClaimType** zdefiniowanego już w sekcji **ClaimsSchema** lub w zasadach, z których dziedziczy ten plik zasad. |
| SubjectNamingInfo | 1:1 | Nazwa podmiotu używana w tokenach. |

Element **Protocol** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Nazwa | Tak | Nazwa prawidłowego protokołu obsługiwanego przez Azure AD B2C, który jest używany jako część profilu technicznego. Możliwe wartości: `OpenIdConnect` lub `SAML2` . `OpenIdConnect`Wartość reprezentuje standard protokołu OpenID Connect Connect 1,0 zgodnie ze specyfikacją OpenID Connect Foundation. `SAML2`Reprezentuje standard protokołu SAML 2,0 zgodnie ze specyfikacją języka Oasis. |

### <a name="metadata"></a>Metadane

Gdy protokół to `SAML` , element metadanych zawiera następujące elementy.

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| IdpInitiatedProfileEnabled | Nie | Wskazuje, czy jest obsługiwany przepływ inicjowany przez dostawcy tożsamości. Możliwe wartości: `true` lub `false` (wartość domyślna). | 
| XmlSignatureAlgorithm | Nie | Metoda, która Azure AD B2C używa do podpisywania odpowiedzi SAML. Możliwe wartości: `Sha256` , `Sha384` , `Sha512` lub `Sha1` . Upewnij się, że algorytm podpisu został skonfigurowany na obu stronach z tą samą wartością. Używaj tylko algorytmu obsługiwanego przez certyfikat. Aby skonfigurować potwierdzenie SAML, zobacz [metadane profilu technicznego wystawcy SAML](saml-issuer-technical-profile.md#metadata). |
| DataEncryptionMethod | Nie | Wskazuje metodę, która Azure AD B2C używa do szyfrowania danych przy użyciu algorytmu Advanced Encryption Standard (AES). Metadane sterują wartością `<EncryptedData>` elementu w odpowiedzi SAML. Możliwe wartości: `Aes256` (wartość domyślna), `Aes192` , `Sha512` lub ` Aes128` . |
| KeyEncryptionMethod| Nie | Wskazuje metodę, za pomocą której Azure AD B2C szyfrować kopię klucza użytego do szyfrowania danych. Metadane sterują wartością  `<EncryptedKey>` elementu w odpowiedzi SAML. Możliwe wartości: ` Rsa15` (domyślnie) — algorytm szyfrowania RSA (Public Key Cryptography standard) (PKCS) w wersji 1,5 ` RsaOaep` . |
| UseDetachedKeys | Nie |  Możliwe wartości: `true` , lub `false` (wartość domyślna). Gdy wartość jest ustawiona na `true` , Azure AD B2C zmienia format szyfrowanych zatwierdzeń. Użycie odłączonych kluczy powoduje dodanie zaszyfrowanego potwierdzenia jako elementu podrzędnego EncrytedAssertion, w przeciwieństwie do EncryptedData. |
| WantsSignedResponses| Nie | Wskazuje, czy Azure AD B2C podpisać `Response` sekcję odpowiedzi SAML. Możliwe wartości: `true` (ustawienie domyślne) lub `false` .  |
| RemoveMillisecondsFromDateTime| Nie | Wskazuje, czy millisconds zostanie usunięta z wartości daty/godziny w odpowiedzi SAML (dotyczy to m.in. IssueInstant, NotBefore, NotOnOrAfter i AuthnInstant). Możliwe wartości: `false` (ustawienie domyślne) lub `true` .  |

### <a name="outputclaims"></a>OutputClaims

Element **OutputClaims** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Oświadczenie outputclaim | 0: n | Nazwa oczekiwanego typu na liście obsługiwanych zasad, dla których zasubskrybuje się Jednostka uzależniona. To zgłoszenie służy jako dane wyjściowe profilu technicznego. |

Element **oświadczenie outputclaim** zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Tak | Odwołanie do elementu **ClaimType** zdefiniowanego już w sekcji **ClaimsSchema** w pliku zasad. |
| DefaultValue | Nie | Wartość domyślna, która może być używana, jeśli wartość jest pusta. |
| PartnerClaimType | Nie | Wysyła oświadczenia w innej nazwie, zgodnie z konfiguracją w definicji elementu ClaimType. |

### <a name="subjectnaminginfo"></a>SubjectNamingInfo

Element **SubjectNameingInfo** umożliwia kontrolowanie wartości podmiotu tokenu:
- **Token JWT** — wartość `sub` żądania. Jest to podmiot zabezpieczeń, w którym token potwierdza informacje, takie jak użytkownik aplikacji. Ta wartość jest niezmienna i nie można jej ponownie przypisać ani ponownie użyć. Może służyć do wykonywania bezpiecznych kontroli autoryzacji, na przykład gdy token jest używany w celu uzyskania dostępu do zasobu. Domyślnie, zgłoszenie podmiotu jest wypełniane IDENTYFIKATORem obiektu użytkownika w katalogu. Aby uzyskać więcej informacji, zobacz [Konfiguracja tokenu, sesji i logowania](session-behavior.md)jednokrotnego.
- **Token SAML** — `<Subject><NameID>` element, który identyfikuje element podmiotu. Format NameId można modyfikować.

Element **SubjectNamingInfo** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Claim | Tak | Odwołanie do **PartnerClaimType** zgłoszenia danych wyjściowych. Oświadczenia wyjściowe muszą być zdefiniowane w kolekcji **OutputClaims** zasad jednostki uzależnionej. |
| Format | Nie | Używane dla jednostek uzależnionych SAML do ustawiania **formatu NameID** zwracanego w potwierdzeniu SAML. |

Poniższy przykład pokazuje, jak zdefiniować jednostkę uzależnioną OpenID Connect Connect. Informacje o nazwie podmiotu są skonfigurowane jako `objectId` :

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
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```
Token JWT zawiera zgłoszenie `sub` z identyfikatorem użytkownika:

```json
{
  ...
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  ...
}
```

Poniższy przykład pokazuje, jak zdefiniować jednostkę uzależnioną SAML. Informacje o nazwie podmiotu są skonfigurowane jako `objectId` , a NameID `format` został udostępniony:

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" Format="urn:oasis:names:tc:SAML:2.0:nameid-format:transient"/>
  </TechnicalProfile>
</RelyingParty>
```
