---
title: Partia zależna — usługa Azure Active Directory B2C | Dokumenty firmy Microsoft
description: Określ element RelyingParty zasad niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 733a33881fe3acc962aeda4b05a1b01be4e148ca
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680359"
---
# <a name="relyingparty"></a>RelyingParty

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Element RelyingParty** określa podróż użytkownika do wymuszenia dla bieżącego żądania do usługi Azure Active Directory B2C (Azure AD B2C). Określa również listę oświadczeń, które wymaga aplikacja jednostki uzależniona (RP) jako część wystawionego tokenu. Aplikacja RP, taka jak aplikacja internetowa, mobilna lub klasyczna, wywołuje plik zasad RP. Plik zasad RP wykonuje określone zadanie, takie jak logowanie się, resetowanie hasła lub edytowanie profilu. Wiele aplikacji może używać tej samej zasady RP, a jedna aplikacja może używać wielu zasad. Wszystkie aplikacje RP otrzymują ten sam token z oświadczeń, a użytkownik przechodzi przez tę samą podróż użytkownika.

W poniższym przykładzie przedstawiono element **RelyingParty** w pliku zasad *B2C_1A_signup_signin:*

```XML
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

Opcjonalny **Element RelyingParty** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| DefaultUserJourney | 1:1 | Domyślna podróż użytkownika dla aplikacji RP. |
| UżytkownikJourneyBehaviors | 0:1 | Zakres zachowań podróży użytkownika. |
| Profil techniczny | 1:1 | Profil techniczny, który jest obsługiwany przez aplikację RP. Profil techniczny zawiera umowę dla aplikacji RP do kontaktu z usługą Azure AD B2C. |

## <a name="defaultuserjourney"></a>DefaultUserJourney

Element `DefaultUserJourney` określa odwołanie do identyfikatora podróży użytkownika, który jest zwykle zdefiniowany w zasadach Base lub Extensions. Poniższe przykłady pokazują rejestracji lub logowania użytkownika podróży określonych w **RelyingParty** element:

*B2C_1A_signup_signin* polityka:

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

*B2C_1A_TrustFrameWorkBase* lub *B2C_1A_TrustFrameworkExtensionPolicy:*

```XML
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
  ...
```

Element **DefaultUserJourney** zawiera następujący atrybut:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Identyfikator odwołania | Tak | Identyfikator podróży użytkownika w zasadach. Aby uzyskać więcej informacji, zobacz [podróże użytkowników](userjourneys.md) |

## <a name="userjourneybehaviors"></a>UżytkownikJourneyBehaviors

**Element UserJourneyBehaviors** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| SingleSignOn (SingleSignOn) | 0:1 | Zakres zachowania sesji logowania jednokrotnego (Logowanie jednokrotne) podróży użytkownika. |
| Typ sesjiExpiry |0:1 | Zachowanie uwierzytelniania sesji. Możliwe `Rolling` wartości: `Absolute`lub . Wartość `Rolling` (domyślna) wskazuje, że użytkownik pozostaje zalogowany tak długo, jak długo użytkownik jest stale aktywny w aplikacji. Wartość `Absolute` wskazuje, że użytkownik jest zmuszony do ponownego uwierzytelnienia po okresie określonym przez okres istnienia sesji aplikacji. |
| SesjaExpiryInSekundy | 0:1 | Okres istnienia pliku cookie sesji usługi Azure AD B2C określony jako liczba całkowita przechowywana w przeglądarce użytkownika po pomyślnym uwierzytelnieniu. |
| Wyzywki podróży | 0:1 | Klucz instrumentacji usługi Azure Application Insights do użycia. |
| ContentDefinitionParameters (Parametry definicji zawartości) | 0:1 | Lista par wartości klucza, które mają być dołączone do identyfikatora URI definicji definicji zawartości. |
|ScenariuszWykonań| 0:1| Obsługiwane tryby wykonywania [JavaScript.](javascript-samples.md) Możliwe `Allow` wartości: `Disallow` lub (domyślnie).

### <a name="singlesignon"></a>SingleSignOn (SingleSignOn)

**Element SingleSignOn** zawiera następujący atrybut:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Zakres | Tak | Zakres zachowania logowania jednokrotnego. Możliwe `Suppressed`wartości: `Tenant` `Application`, `Policy`, , lub . Wartość `Suppressed` wskazuje, że zachowanie jest pomijane, a użytkownik jest zawsze monitowany o wybór dostawcy tożsamości.  Wartość `Tenant` wskazuje, że zachowanie jest stosowane do wszystkich zasad w dzierżawie. Na przykład użytkownik przechodzący przez dwie podróże zasad dla dzierżawcy nie jest monitowany o wybór dostawcy tożsamości. Wartość `Application` wskazuje, że zachowanie jest stosowane do wszystkich zasad dla aplikacji składającej żądanie. Na przykład użytkownik przechodzący przez dwie podróże zasad dla aplikacji nie jest monitowany o wybór dostawcy tożsamości. Wartość `Policy` wskazuje, że zachowanie dotyczy tylko zasad. Na przykład użytkownik przechodzący przez dwie podróże zasad dla struktury zaufania jest monitowany o wybór dostawcy tożsamości podczas przełączania między zasadami. |
| KeepAliveInDays (KeepAliveInDays) | Tak | Określa, jak długo użytkownik pozostaje zalogowany. Ustawienie wartości 0 powoduje wyłączenie funkcji KMSI. Aby uzyskać więcej informacji, zobacz [Nie loguj się .](custom-policy-keep-me-signed-in.md) |
|EnforceIdTokenHintOnLogout| Nie|  Wymuś przekazanie wcześniej wystawionego tokenu identyfikatora do punktu końcowego wylogowania jako wskazówka dotycząca bieżącej uwierzytelnionej sesji użytkownika końcowego z klientem. Możliwe wartości: `false` (domyślnie) lub `true`. Aby uzyskać więcej informacji, zobacz [Logowanie do sieci Web za pomocą openid connect](openid-connect.md).  |


## <a name="journeyinsights"></a>Wyzywki podróży

**Element JourneyInsights** zawiera następujące atrybuty:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| TelemetriaInżynia | Tak | Wartość musi `ApplicationInsights`być . |
| InstrumentationKey (Narzędzie instrumentacyjne) | Tak | Ciąg, który zawiera klucz instrumentacji dla elementu wglądu w aplikację. |
| Tryb deweloperski | Tak | Możliwe `true` wartości: `false`lub . Jeśli `true`usługa Application Insights przyspiesza dane telemetryczne za pośrednictwem potoku przetwarzania. To ustawienie jest dobre dla rozwoju, ale ograniczone w dużych ilościach Szczegółowe dzienniki aktywności są przeznaczone tylko do pomocy w opracowywaniu zasad niestandardowych. Nie należy używać trybu rozwoju w produkcji. Dzienniki zbierają wszystkie oświadczenia wysyłane do i od dostawców tożsamości podczas tworzenia. Jeśli jest używany w produkcji, deweloper przejmuje odpowiedzialność za informacje umożliwiające identyfikację użytkownika (informacje umożliwiające identyfikację prywatną) zebrane w dzienniku usługi App Insights, który jest ich właścicielem. Te szczegółowe dzienniki są zbierane tylko `true`wtedy, gdy ta wartość jest ustawiona na .|
| KlientWłasny | Tak | Możliwe `true` wartości: `false`lub . Jeśli `true`program , wysyła skrypt po stronie klienta usługi Application Insights do śledzenia widoku strony i błędów po stronie klienta. |
| ServerEnabled (Usługa ServerEnabled) | Tak | Możliwe `true` wartości: `false`lub . Jeśli `true`, wysyła istniejący UserJourneyRecorder JSON jako zdarzenie niestandardowe do usługi Application Insights. |
| TelemetryVersion | Tak | Wartość musi `1.0.0`być . |

Aby uzyskać więcej informacji, zobacz [Zbieranie dzienników](troubleshoot-with-application-insights.md)

## <a name="contentdefinitionparameters"></a>ContentDefinitionParameters (Parametry definicji zawartości)

Za pomocą zasad niestandardowych w usłudze Azure AD B2C, można wysłać parametr w ciągu zapytania. Przekazanie parametru do punktu końcowego HTML pozwala na dynamiczną zmianę zawartość strony. Na podstawie parametru przekazywanego z aplikacji internetowej lub aplikacji mobilnej można na przykład zmienić obraz tła na stronie rejestracji lub logowania usługi Azure AD B2C. Usługa Azure AD B2C przekazuje parametry ciągu zapytania do dynamicznego pliku HTML, takiego jak plik aspx.

Poniższy przykład przekazuje `campaignId` parametr o `hawaii` nazwie o wartości w ciągu kwerendy:

`https://login.microsoft.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?pB2C_1A_signup_signin&client_id=a415078a-0402-4ce3-a9c6-ec1947fcfb3f&nonce=defaultNonce&redirect_uri=http%3A%2F%2Fjwt.io%2F&scope=openid&response_type=id_token&prompt=login&campaignId=hawaii`

**Element ContentDefinitionParameters** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Parametr ContentDefinitionParameter | 0:n | Ciąg, który zawiera parę wartości klucza, który jest dołączany do ciągu kwerendy definicji zawartości załadować identyfikator URI. |

**Element ContentDefinitionParameter** zawiera następujący atrybut:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Nazwa | Tak | Nazwa pary wartości klucza. |

Aby uzyskać więcej informacji, zobacz [Konfigurowanie interfejsu użytkownika z zawartością dynamiczną przy użyciu zasad niestandardowych](custom-policy-ui-customization.md#configure-dynamic-custom-page-content-uri)

## <a name="technicalprofile"></a>Profil techniczny

Element **TechnicalProfile** zawiera następujący atrybut:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Identyfikator | Tak | Wartość musi `PolicyProfile`być . |

**Profil techniczny** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| DisplayName | 1:1 | Ciąg zawierający nazwę profilu technicznego. |
| Opis | 0:1 | Ciąg, który zawiera opis profilu technicznego. |
| Protocol (Protokół) | 1:1 | Protokół używany dla federacji. |
| Metadane | 0:1 | Kolekcja *par klucz/wartość elementu* używane przez protokół do komunikowania się z punktem końcowym w trakcie transakcji w celu skonfigurowania interakcji między jednostką uzależniającą a innymi uczestnikami społeczności. |
| OutputClaims (Roszczenia wyjściowe) | 1:1 | Lista typów oświadczeń, które są traktowane jako dane wyjściowe w profilu technicznym. Każdy z tych elementów zawiera odwołanie do **claimtype** już zdefiniowane w **ClaimsSchema** sekcji lub w zasadach, z których dziedziczy ten plik zasad. |
| ObiektNamingInfo | 1:1 | Nazwa podmiotu używana w tokenach. |

Element **Protokołu** zawiera następujący atrybut:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Nazwa | Tak | Nazwa prawidłowego protokołu obsługiwanego przez usługę Azure AD B2C, który jest używany jako część profilu technicznego. Możliwe `OpenIdConnect` wartości: `SAML2`lub . Wartość `OpenIdConnect` reprezentuje standard protokołu OpenID Connect 1.0 zgodnie ze specyfikacją fundamentu OpenID. Reprezentuje `SAML2` standard protokołu SAML 2.0 zgodnie ze specyfikacją OASIS. |

## <a name="outputclaims"></a>OutputClaims (Roszczenia wyjściowe)

**OutputClaims** element zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| WynikClaim | 0:n | Nazwa typu oczekiwanego oświadczenia na liście obsługiwanych zasad, do których subskrybuje strona uzależniona. To oświadczenie służy jako wyjście dla profilu technicznego. |

**OutputClaim** element zawiera następujące atrybuty:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Zastrzeżenie rodzaju żądań | Tak | Odwołanie do **claimtype** już zdefiniowane w **ClaimsSchema** sekcji w pliku zasad. |
| Defaultvalue | Nie | Wartość domyślna, która może być używana, jeśli wartość oświadczenia jest pusta. |
| Typ partnera | Nie | Wysyła oświadczenie w innej nazwie skonfigurowane w definicji ClaimType. |

### <a name="subjectnaminginfo"></a>ObiektNamingInfo

Za pomocą **elementu SubjectNameingInfo** można kontrolować wartość tematu tokenu:
- **Token JWT** `sub` — oświadczenie. Jest to podmiot zabezpieczeń, o którym token potwierdza informacje, takie jak użytkownik aplikacji. Ta wartość jest niezmienna i nie można jej ponownie przypisać ani ponownie. Może służyć do wykonywania kontroli bezpiecznej autoryzacji, takich jak gdy token jest używany do uzyskiwania dostępu do zasobu. Domyślnie oświadczenie podmiotu jest wypełniane identyfikatorem obiektu użytkownika w katalogu. Aby uzyskać więcej informacji, zobacz [Token, konfiguracja sesji i logowania jednokrotnego](session-behavior.md).
- **SAML token** `<Subject><NameID>` - element, który identyfikuje element podmiotu.

Element **SubjectNamingInfo** zawiera następujący atrybut:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Claimtype | Tak | Odwołanie do produktu oświadczenia **PartnerClaimType**. Oświadczenia danych wyjściowych muszą być zdefiniowane w kolekcji **outputclaims** zasad jednostki uzależniającej. |

W poniższym przykładzie pokazano, jak zdefiniować jednostkę uzależniającą OpenID Connect. Informacje o nazwie podmiotu `objectId`są skonfigurowane jako :

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
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```
Token JWT zawiera `sub` oświadczenie z identyfikatorem objectId użytkownika:

```JSON
{
  ...
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  ...
}
```
