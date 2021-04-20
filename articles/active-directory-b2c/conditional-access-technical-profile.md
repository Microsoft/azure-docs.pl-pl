---
title: Profile techniczne dostępu warunkowego w zasadach niestandardowych
titleSuffix: Azure AD B2C
description: Dokumentacja zasad niestandardowych dla profilów technicznych dostępu warunkowego w Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/19/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ebdc1c9c92f6e3debf08cb640e46424c4ad9d5ad
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107721890"
---
# <a name="define-a-conditional-access-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiowanie profilu technicznego dostępu warunkowego w Azure Active Directory B2C zasad niestandardowych

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory dostępu warunkowego (Azure AD) jest narzędziem używanym przez usługę Azure AD B2C do dosyłania sygnałów, podejmowania decyzji i wymuszania zasad organizacyjnych. Automatyzacja oceny ryzyka przy użyciu warunków zasad oznacza, że ryzykowne logowania są identyfikowane i korygowane lub blokowane jednocześnie.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Protokół

Atrybut **Name** elementu **Protocol** musi być ustawiony na `Proprietary` . Atrybut **procedury** obsługi musi zawierać w pełni kwalifikowaną nazwę zestawu obsługi protokołu, który jest używany przez Azure AD B2C:

```
Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

W poniższym przykładzie pokazano profil techniczny dostępu warunkowego:

```XML
<TechnicalProfile Id="ConditionalAccessEvaluation">
  <DisplayName>Conditional Access Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="OperationType">Evaluation</Item>
  </Metadata>
```

## <a name="conditional-access-evaluation"></a>Ocena dostępu warunkowego

Dla każdego logowania użytkownik Azure AD B2C wszystkie zasady i upewnia się, że wszystkie wymagania zostały spełnione przed udzieleniem użytkownikowi dostępu. "Blokuj dostęp" zastępuje wszystkie inne ustawienia konfiguracji. Tryb **oceny** profilu technicznego dostępu warunkowego ocenia sygnały zebrane przez Azure AD B2C podczas logowania przy użyciu konta lokalnego. Wynikiem profilu technicznego dostępu warunkowego jest zestaw oświadczeń wynikających z oceny dostępu warunkowego. Zasady Azure AD B2C wykorzystują te oświadczenia w następnym kroku aranżacji, aby podjąć akcję, taką jak zablokowanie użytkownika lub wyzłosenie użytkownika za pomocą uwierzytelniania wieloskładnikowego. Dla tego trybu można skonfigurować następujące opcje.

### <a name="metadata"></a>Metadane

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| OperationType | Tak | Musi to być **ocena**.  |

### <a name="input-claims"></a>Oświadczenia wejściowe

Element **InputClaims** zawiera listę oświadczeń do wysłania do dostępu warunkowego. Możesz również zamapować nazwę oświadczenia na nazwę zdefiniowaną w profilu technicznym dostępu warunkowego.

| ClaimReferenceId | Wymagane | Typ danych | Opis |
| --------- | -------- | ----------- |----------- |
| UserId | Tak | ciąg | Identyfikator użytkownika, który się dodaje. |
| AuthenticationMethodsUsed | Tak |Stringcollection | Lista metod używanych przez użytkownika do logowania. Możliwe wartości: `Password` , i `OneTimePasscode` . |
| IsFederated | Tak |boolean | Wskazuje, czy użytkownik zalogował się przy użyciu konta federowego. Musi to być wartość `false` . |
| IsMfaRegistered | Tak |boolean | Wskazuje, czy użytkownik zaestrł już numer telefonu do uwierzytelniania wieloskładnikowego. |


**Element InputClaimsTransformations** może zawierać kolekcję elementów **InputClaimsTransformation,** które są używane do modyfikowania oświadczeń wejściowych lub generowania nowych przed wysłaniem ich do usługi dostępu warunkowego.

### <a name="output-claims"></a>Oświadczenia wyjściowe

**OutputClaims** element zawiera listę oświadczeń wygenerowanych przez ConditionalAccessProtocolProvider. Możesz również zamapować nazwę swojego oświadczenia na nazwę zdefiniowaną poniżej.

| ClaimReferenceId | Wymagane | Typ danych | Opis |
| --------- | -------- | ----------- |----------- |
| Wyzwania | Tak |Stringcollection | Lista akcji w celu skorygowania zidentyfikowanego zagrożenia. Możliwe wartości: `block` |
| MultiConditionalAccessStatus | Tak | Stringcollection |  |

Element **OutputClaimsTransformations** może zawierać kolekcję elementów **OutputClaimsTransformation,** które są używane do modyfikowania oświadczeń wyjściowych lub generowania nowych.

### <a name="example-evaluation"></a>Przykład: Ocena

W poniższym przykładzie pokazano profil techniczny dostępu warunkowego, który jest używany do oceny zagrożenia logowania.

```XML
<TechnicalProfile Id="ConditionalAccessEvaluation">
  <DisplayName>Conditional Access Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="OperationType">Evaluation</Item>
  </Metadata>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="IsMfaRegisteredCT" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="UserId" />
    <InputClaim ClaimTypeReferenceId="AuthenticationMethodsUsed" />
    <InputClaim ClaimTypeReferenceId="IsFederated" DefaultValue="false" />
    <InputClaim ClaimTypeReferenceId="IsMfaRegistered" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="conditionalAccessClaimCollection" PartnerClaimType="Challenges" />
    <OutputClaim ClaimTypeReferenceId="ConditionalAccessStatus" PartnerClaimType="MultiConditionalAccessStatus" />
  </OutputClaims>
</TechnicalProfile>
```

## <a name="remediation"></a>Korekty

Tryb **korygowania** profilu technicznego dostępu warunkowego informuje Azure AD B2C, że zidentyfikowane zagrożenie logowaniem zostanie skorygowane. Następujące opcje można skonfigurować dla trybu korygowania.

### <a name="metadata"></a>Metadane

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| OperationType | Tak | Musi to być **korygowanie**.  |

### <a name="input-claims"></a>Oświadczenia wejściowe

Element **InputClaims** zawiera listę oświadczeń do wysłania do dostępu warunkowego. Możesz również zamapować nazwę oświadczenia na nazwę zdefiniowaną w profilu technicznym dostępu warunkowego.

| ClaimReferenceId | Wymagane | Typ danych | Opis |
| --------- | -------- | ----------- |----------- |
| WyzwaniaZadowolony | Tak | Stringcollection| Lista spełnionych wyzwań w celu skorygowania zidentyfikowanego zagrożenia jako zwrotu z trybu oceny, oświadczenia wyzwania.|


Element **InputClaimsTransformations** może zawierać kolekcję elementów **InputClaimsTransformation,** które są używane do modyfikowania oświadczeń wejściowych lub generowania nowych przed wywołaniem usługi dostępu warunkowego.

### <a name="output-claims"></a>Oświadczenia wyjściowe

Dostawca protokołu dostępu warunkowego nie zwraca żadnych **oświadczeń OutputClaim,** więc nie ma potrzeby określania oświadczeń wyjściowych. Można jednak uwzględnić oświadczenia, które nie są zwracane przez dostawcę protokołu dostępu warunkowego, o ile atrybut zostanie `DefaultValue` ustawiony.

Element **OutputClaimsTransformations** może zawierać kolekcję elementów **OutputClaimsTransformation,** które są używane do modyfikowania oświadczeń wyjściowych lub generowania nowych.

### <a name="example-remediation"></a>Przykład: Korygowanie

W poniższym przykładzie pokazano profil techniczny dostępu warunkowego, który jest używany do korygowania zagrożenia logowania.

```xml
<TechnicalProfile Id="ConditionalAccessRemediation">
  <DisplayName>Conditional Access Remediation</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
  <Metadata>
    <Item Key="OperationType">Remediation</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="conditionalAccessClaimCollection" PartnerClaimType="ChallengesSatisfied" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [dodać dostęp warunkowy do przepływów użytkownika w Azure Active Directory B2C](conditional-access-user-flow.md).
