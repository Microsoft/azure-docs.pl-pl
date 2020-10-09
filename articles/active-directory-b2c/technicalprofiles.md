---
title: TechnicalProfiles
titleSuffix: Azure AD B2C
description: Określ element TechnicalProfiles zasad niestandardowych w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a788134f64066b0469d34fbfbacacd8c45438bde
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85203150"
---
# <a name="technicalprofiles"></a>TechnicalProfiles

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Element **TechnicalProfiles** zawiera zestaw profilów technicznych obsługiwanych przez dostawcę usług. Każdy dostawca oświadczeń musi mieć co najmniej jeden profil techniczny, który określa punkty końcowe i protokoły wymagane do komunikowania się z dostawcą oświadczeń. Dostawca oświadczeń może mieć wiele profilów technicznych.

```xml
<ClaimsProvider>
  <DisplayName>Display name</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Technical profile identifier">
      <DisplayName>Display name of technical profile</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">URL of service</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
      </Metadata>
      <InputTokenFormat>JWT</InputTokenFormat>
      <OutputTokenFormat>JWT</OutputTokenFormat>
      <CryptographicKeys>
        <Key ID="Key identifier" StorageReferenceId="Storage key container identifier"/>
        ...
      </CryptographicKeys>
      <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="Claims transformation identifier" />
        ...
      <InputClaimsTransformations>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" DefaultValue="givenName" PartnerClaimType="firstName" />
        ...
      </InputClaims>
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="givenName" DefaultValue="givenName" PartnerClaimType="firstName" />
        ...
      </PersistedClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" DefaultValue="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
      </OutputClaims>
      <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="Claims transformation identifier" />
        ...
      <OutputClaimsTransformations>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="Technical profile identifier" />
        ...
      </ValidationTechnicalProfiles>
      <SubjectNamingInfo ClaimType="Claim type identifier" />
      <IncludeTechnicalProfile ReferenceId="Technical profile identifier" />
      <UseTechnicalProfileForSessionManagement ReferenceId="Technical profile identifier" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

Element **profilu technicznym** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
|---------|---------|---------|
| Id | Tak | Unikatowy identyfikator profilu technicznego. Profil techniczny może być przywoływany przy użyciu tego identyfikatora z innych elementów w pliku zasad. Na przykład **OrchestrationSteps** i **ValidationTechnicalProfile**. |

**Profilu technicznym** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Obszar | 0:1 | Nazwa domeny profilu technicznego. Na przykład jeśli Twój profil techniczny określa dostawcę tożsamości w serwisie Facebook, nazwa domeny to Facebook.com. |
| Nazwa wyświetlana | 1:1 | Nazwa profilu technicznego, który może być wyświetlany użytkownikom. |
| Opis | 0:1 | Opis profilu technicznego, który może być wyświetlany użytkownikom. |
| Protokół | 0:1 | Protokół używany do komunikacji z drugą stroną. |
| Metadane | 0:1 | Kolekcja par klucz/wartość, które są wykorzystywane przez protokół do komunikacji z punktem końcowym w trakcie transakcji. |
| InputTokenFormat | 0:1 | Format tokenu wejściowego. Możliwe wartości: `JSON` , `JWT` , `SAML11` lub `SAML2` . `JWT`Wartość reprezentuje token sieci Web JSON zgodnie ze specyfikacją IETF. `SAML11`Wartość reprezentuje token zabezpieczający protokołu SAML 1,1 zgodnie ze specyfikacją języka Oasis.  `SAML2`Wartość reprezentuje token zabezpieczający protokołu SAML 2,0 zgodnie ze specyfikacją języka Oasis. |
| OutputTokenFormat | 0:1 | Format tokenu wyjściowego. Możliwe wartości: `JSON` , `JWT` , `SAML11` lub `SAML2` . |
| CryptographicKeys | 0:1 | Lista kluczy kryptograficznych, które są używane w profilu technicznym. |
| InputClaimsTransformations | 0:1 | Lista wcześniej zdefiniowanych odwołań do transformacji oświadczeń, które należy wykonać przed wysłaniem jakichkolwiek oświadczeń do dostawcy oświadczeń lub jednostki uzależnionej. |
| InputClaims | 0:1 | Lista wcześniej zdefiniowanych odwołań do typów zgłoszeń, które są pobierane jako dane wejściowe w profilu technicznym. |
| PersistedClaims | 0:1 | Lista wcześniej zdefiniowanych odwołań do typów oświadczeń, które są utrwalane przez dostawcę oświadczeń odnoszących się do profilu technicznego. |
| DisplayClaims | 0:1 | Lista wcześniej zdefiniowanych odwołań do typów oświadczeń, które są prezentowane przez dostawcę oświadczeń, które odnoszą się do [profilu technicznego z własnym potwierdzeniem](self-asserted-technical-profile.md). Funkcja DisplayClaims jest obecnie w **wersji zapoznawczej**. |
| OutputClaims | 0:1 | Lista wcześniej zdefiniowanych odwołań do typów zgłoszeń, które są pobierane jako dane wyjściowe w profilu technicznym. |
| OutputClaimsTransformations | 0:1 | Lista wcześniej zdefiniowanych odwołań do transformacji oświadczeń, które należy wykonać po odebraniu oświadczeń od dostawcy oświadczeń. |
| ValidationTechnicalProfiles | 0: n | Lista odwołań do innych profilów technicznych używanych przez profil techniczny do celów weryfikacji. Aby uzyskać więcej informacji, zobacz temat [Sprawdzanie poprawności profilu technicznego](validation-technical-profile.md)|
| SubjectNamingInfo | 0:1 | Kontroluje produkcję nazwy podmiotu w tokenach, w których nazwa podmiotu jest określona oddzielnie od oświadczeń. Na przykład uwierzytelnianie OAuth lub SAML.  |
| IncludeInSso | 0:1 |  Określa, czy użycie tego profilu technicznego ma mieć zastosowanie do działania logowania jednokrotnego (SSO) dla sesji lub zamiast tego wymagać jawnej interakcji. Ten element jest prawidłowy tylko w profilach SelfAsserted używanych w profilu technicznym weryfikacji. Możliwe wartości: `true` (ustawienie domyślne) lub `false` . |
| IncludeClaimsFromTechnicalProfile | 0:1 | Identyfikator profilu technicznego, z którego mają zostać dodane wszystkie oświadczenia wejściowe i wyjściowe do tego profilu technicznego. Profil techniczny, do którego istnieje odwołanie, musi być zdefiniowany w tym samym pliku zasad. |
| IncludeTechnicalProfile |0:1 | Identyfikator profilu technicznego, z którego mają zostać dodane wszystkie dane do tego profilu technicznego. |
| UseTechnicalProfileForSessionManagement | 0:1 | Inny profil techniczny, który ma być używany na potrzeby zarządzania sesją. |
|EnabledForUserJourneys| 0:1 |Kontroluje, czy profil techniczny jest wykonywany w podróży użytkownika.  |

## <a name="protocol"></a>Protokół

Element **Protocol** zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Nazwa | Tak | Nazwa prawidłowego protokołu obsługiwanego przez Azure AD B2C, który jest używany jako część profilu technicznego. Możliwe wartości: `OAuth1` , `OAuth2` ,,, `SAML2` `OpenIdConnect` `Proprietary` lub `None` . |
| Program obsługi | Nie | Jeśli nazwa protokołu jest ustawiona na `Proprietary` , określ w pełni kwalifikowaną nazwę zestawu, który jest używany przez Azure AD B2C do określenia procedury obsługi protokołu. |

## <a name="metadata"></a>Metadane

Element **Metadata** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Element | 0: n | Metadane odnoszące się do profilu technicznego. Każdy typ profilu technicznego ma inny zestaw elementów metadanych. Aby uzyskać więcej informacji, zobacz sekcję typy profilów technicznych. |

### <a name="item"></a>Element

Element **Item** elementu **Metadata** zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Klucz | Tak | Klucz metadanych. Zobacz każdy typ profilu technicznego, aby wyświetlić listę elementów metadanych. |

## <a name="cryptographickeys"></a>CryptographicKeys

Element **CryptographicKeys** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Klucz | 1: n | Klucz kryptograficzny używany w tym profilu technicznym. |

### <a name="key"></a>Klucz

Element **Key** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Id | Nie | Unikatowy identyfikator konkretnej pary kluczy, do której odwołuje się inne elementy w pliku zasad. |
| Identyfikatorze storagereferenceid | Tak | Identyfikator kontenera klucza magazynu, do którego odwołuje się inne elementy w pliku zasad. |

## <a name="inputclaimstransformations"></a>InputClaimsTransformations

Element **InputClaimsTransformations** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| InputClaimsTransformation | 1: n | Identyfikator transformacji oświadczeń, która powinna zostać wykonana przed wysłaniem jakichkolwiek oświadczeń do dostawcy oświadczeń lub jednostki uzależnionej. Transformacja oświadczeń może służyć do modyfikowania istniejących oświadczeń ClaimsSchema lub generowania nowych. |

### <a name="inputclaimstransformation"></a>InputClaimsTransformation

Element **InputClaimsTransformation** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Identyfikator odwołania | Tak | Identyfikator transformacji oświadczeń zdefiniowany już w pliku zasad lub nadrzędnym pliku zasad. |

## <a name="inputclaims"></a>InputClaims

Element **InputClaims** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Oświadczenie inputclaim | 1: n | Oczekiwany typ zgłoszenia wejściowego. |

### <a name="inputclaim"></a>Oświadczenie inputclaim

Element **oświadczenie inputclaim** zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Tak | Identyfikator typu "Claim" jest już zdefiniowany w sekcji ClaimsSchema w pliku zasad lub nadrzędnym pliku zasad. |
| DefaultValue | Nie | Wartość domyślna, która ma zostać użyta do utworzenia żądania, jeśli nie istnieje w nim zastrzeżenie wskazywane przez ClaimTypeReferenceId, aby uzyskane wystąpienie może być używane jako oświadczenie inputclaim przez profil techniczny. |
| PartnerClaimType | Nie | Identyfikator typu dla partnera zewnętrznego, na który jest mapowany określony typ roszczeń zasad. Jeśli atrybut PartnerClaimType nie zostanie określony, określony typ roszczeń zasad jest mapowany na typ wiązania partnera o tej samej nazwie. Użyj tej właściwości, gdy nazwa typu usługi jest inna od drugiej strony. Na przykład nazwa pierwszego wystąpienia to "imięname", podczas gdy Partner używa roszczeń o nazwie "first_name". |

## <a name="displayclaims"></a>DisplayClaims

Element **DisplayClaims** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| DisplayClaim | 1: n | Oczekiwany typ zgłoszenia wejściowego. |

Funkcja DislayClaims jest obecnie w **wersji zapoznawczej**.

### <a name="displayclaim"></a>DisplayClaim

Element **DisplayClaim** zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Nie | Identyfikator typu "Claim" jest już zdefiniowany w sekcji ClaimsSchema w pliku zasad lub nadrzędnym pliku zasad. |
| DisplayControlReferenceId | Nie | Identyfikator [kontrolki wyświetlania](display-controls.md) zdefiniowany już w sekcji ClaimsSchema w pliku zasad lub nadrzędnym pliku zasad. |
| Wymagane | Nie | Wskazuje, czy jest wymagane żądanie wyświetlania. |

**DisplayClaim** wymaga określenia albo `ClaimTypeReferenceId` `DisplayControlReferenceId` .

### <a name="persistedclaims"></a>PersistedClaims

Element **PersistedClaims** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| PersistedClaim | 1: n | Typ zgłoszenia, który ma być trwały. |

### <a name="persistedclaim"></a>PersistedClaim

Element **PersistedClaim** zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Tak | Identyfikator typu "Claim" jest już zdefiniowany w sekcji ClaimsSchema w pliku zasad lub nadrzędnym pliku zasad. |
| DefaultValue | Nie | Wartość domyślna, która ma zostać użyta do utworzenia żądania, jeśli nie istnieje w nim zastrzeżenie wskazywane przez ClaimTypeReferenceId, aby uzyskane wystąpienie może być używane jako oświadczenie inputclaim przez profil techniczny. |
| PartnerClaimType | Nie | Identyfikator typu dla partnera zewnętrznego, na który jest mapowany określony typ roszczeń zasad. Jeśli atrybut PartnerClaimType nie zostanie określony, określony typ roszczeń zasad jest mapowany na typ wiązania partnera o tej samej nazwie. Użyj tej właściwości, gdy nazwa typu usługi jest inna od drugiej strony. Na przykład nazwa pierwszego wystąpienia to "imięname", podczas gdy Partner używa roszczeń o nazwie "first_name". |

## <a name="outputclaims"></a>OutputClaims

Element **OutputClaims** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Oświadczenie outputclaim | 1: n | Oczekiwany typ zgłoszenia wyjściowego. |

### <a name="outputclaim"></a>Oświadczenie outputclaim

Element **oświadczenie outputclaim** zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Tak | Identyfikator typu "Claim" jest już zdefiniowany w sekcji ClaimsSchema w pliku zasad lub nadrzędnym pliku zasad. |
| DefaultValue | Nie | Wartość domyślna, która ma zostać użyta do utworzenia żądania, jeśli nie istnieje w nim zastrzeżenie wskazywane przez ClaimTypeReferenceId, aby uzyskane wystąpienie może być używane jako oświadczenie inputclaim przez profil techniczny. |
|AlwaysUseDefaultValue |Nie |Wymuś użycie wartości domyślnej.  |
| PartnerClaimType | Nie | Identyfikator typu dla partnera zewnętrznego, na który jest mapowany określony typ roszczeń zasad. Jeśli atrybut PartnerClaimType nie zostanie określony, określony typ roszczeń zasad jest mapowany na typ wiązania partnera o tej samej nazwie. Użyj tej właściwości, gdy nazwa typu usługi jest inna od drugiej strony. Na przykład nazwa pierwszego wystąpienia to "imięname", podczas gdy Partner używa roszczeń o nazwie "first_name". |

## <a name="outputclaimstransformations"></a>OutputClaimsTransformations

Element **OutputClaimsTransformations** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| OutputClaimsTransformation | 1: n | Identyfikatory przekształceń oświadczeń, które należy wykonać przed wysłaniem oświadczeń do dostawcy oświadczeń lub jednostki uzależnionej. Transformacja oświadczeń może służyć do modyfikowania istniejących oświadczeń ClaimsSchema lub generowania nowych. |

### <a name="outputclaimstransformation"></a>OutputClaimsTransformation

Element **OutputClaimsTransformation** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Identyfikator odwołania | Tak | Identyfikator transformacji oświadczeń zdefiniowany już w pliku zasad lub nadrzędnym pliku zasad. |

## <a name="validationtechnicalprofiles"></a>ValidationTechnicalProfiles

Element **ValidationTechnicalProfiles** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1: n | Identyfikatory profilów technicznych, które są używane, sprawdzają niektóre lub wszystkie oświadczenia wynikowe profilu technicznego. Wszystkie oświadczenia wejściowe profilu technicznego, do którego istnieje odwołanie, muszą pojawić się w oświadczeniach wyjściowych w profilu technicznym odwołania. |

### <a name="validationtechnicalprofile"></a>ValidationTechnicalProfile

Element **ValidationTechnicalProfile** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Identyfikator odwołania | Tak | Identyfikator profilu technicznego jest już zdefiniowany w pliku zasad lub nadrzędnym pliku zasad. |

## <a name="subjectnaminginfo"></a>SubjectNamingInfo

**SubjectNamingInfo** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Claim | Tak | Identyfikator typu "Claim" jest już zdefiniowany w sekcji ClaimsSchema w pliku zasad. |

## <a name="includetechnicalprofile"></a>IncludeTechnicalProfile

Element **IncludeTechnicalProfile** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Identyfikator odwołania | Tak | Identyfikator profilu technicznego zdefiniowany już w pliku zasad lub nadrzędnym pliku zasad. |

## <a name="usetechnicalprofileforsessionmanagement"></a>UseTechnicalProfileForSessionManagement

Element **UseTechnicalProfileForSessionManagement** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Identyfikator odwołania | Tak | Identyfikator profilu technicznego jest już zdefiniowany w pliku zasad lub nadrzędnym pliku zasad. |

## <a name="enabledforuserjourneys"></a>EnabledForUserJourneys

**ClaimsProviderSelections** w podróży użytkownika definiuje listę opcji wyboru dostawcy oświadczeń i ich kolejność. Za pomocą filtru **EnabledForUserJourneys** element, który jest dostępny dla użytkownika. Element **EnabledForUserJourneys** zawiera jedną z następujących wartości:

- **Zawsze**należy wykonać profil techniczny.
- **Nigdy**, Pomiń profil techniczny.
- **OnClaimsExistence** wykonać tylko wtedy, gdy istnieje określone określone w profilu technicznym.
- **OnItemExistenceInStringCollectionClaim**, wykonaj tylko wtedy, gdy element istnieje w ramach żądania kolekcji ciągów.
- **OnItemAbsenceInStringCollectionClaim** wykonać tylko wtedy, gdy element nie istnieje w ramach żądania kolekcji ciągów.

Korzystanie z **OnClaimsExistence**, **OnItemExistenceInStringCollectionClaim** lub **OnItemAbsenceInStringCollectionClaim**wymaga podania następujących metadanych: **ClaimTypeOnWhichToEnable** określa typ zgłoszenia, który ma być oceniany, **ClaimValueOnWhichToEnable** określa wartość, która ma być porównana.

Następujący profil techniczny jest wykonywany tylko wtedy, gdy kolekcja ciągów **skojarzeni** zawiera wartość `facebook.com` :

```xml
<TechnicalProfile Id="UnLink-Facebook-OAUTH">
  <DisplayName>Unlink Facebook</DisplayName>
...
    <Metadata>
      <Item Key="ClaimTypeOnWhichToEnable">identityProviders</Item>
      <Item Key="ClaimValueOnWhichToEnable">facebook.com</Item>
    </Metadata>
...
  <EnabledForUserJourneys>OnItemExistenceInStringCollectionClaim</EnabledForUserJourneys>
</TechnicalProfile>
```
