---
title: Zdefiniuj profil techniczny transformacji oświadczeń
titleSuffix: Azure AD B2C
description: Zdefiniuj profil techniczny transformacji oświadczeń w zasadach niestandardowych w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/13/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6553b9ec120ca0e1e479b400495b61bc68c88cf3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85201212"
---
# <a name="define-a-claims-transformation-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Zdefiniuj profil techniczny transformacji oświadczeń w zasadach niestandardowych Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Profil techniczny przekształcania oświadczeń umożliwia wywoływanie przekształceń oświadczeń wyjściowych w celu manipulowania wartościami oświadczeń, weryfikowania oświadczeń lub ustawiania wartości domyślnych dla zestawu oświadczeń wyjściowych.

## <a name="protocol"></a>Protokół

Atrybut **name** elementu **Protocol** musi być ustawiony na `Proprietary` . Atrybut **programu obsługi** musi zawierać w pełni kwalifikowaną nazwę zestawu programu obsługi protokołu, który jest używany przez Azure AD B2C: `Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null` .

Poniższy przykład przedstawia profil techniczny przekształcenia oświadczeń:

```xml
<TechnicalProfile Id="Facebook-OAUTH-UnLink">
    <DisplayName>Unlink Facebook</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="output-claims"></a>Oświadczenia wyjściowe

Element **OutputClaims** jest obowiązkowy. Należy podać co najmniej jedno zgłoszenie wyjściowe zwrócone przez profil techniczny. Poniższy przykład pokazuje, jak ustawić wartości domyślne w oświadczeniach wyjściowych:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="ageGroup" DefaultValue="Undefined" />
  <OutputClaim ClaimTypeReferenceId="ageGroupValueChanged" DefaultValue="false" />
</OutputClaims>
```

## <a name="output-claims-transformations"></a>Przekształcenia oświadczeń wyjściowych

Element **OutputClaimsTransformations** może zawierać kolekcję elementów **OutputClaimsTransformation** , które są używane do modyfikowania oświadczeń lub generowania nowych. Poniższy profil techniczny wywołuje transformację oświadczeń **RemoveAlternativeSecurityIdByIdentityProvider** . Ta transformacja oświadczeń usuwa identyfikację społeczną z kolekcji **AlternativeSecurityIds**. Oświadczenia wyjściowe tego profilu technicznego to **identityProvider2**, który jest ustawiony na `facebook.com` i **AlternativeSecurityIds**, który zawiera listę tożsamości społeczności skojarzonych z tym użytkownikiem po usunięciu tożsamości Facebook.com.

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="RemoveAlternativeSecurityIdByIdentityProvider"
TransformationMethod="RemoveAlternativeSecurityIdByIdentityProvider">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="IdentityProvider2"
TransformationClaimType="identityProvider" />
      <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds"
TransformationClaimType="collection" />
    </InputClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds"
TransformationClaimType="collection" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
...
<TechnicalProfile Id="Facebook-OAUTH-UnLink">
    <DisplayName>Unlink Facebook</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="identityProvider2" DefaultValue="facebook.com" AlwaysUseDefaultValue="true" />
    </OutputClaims>
    <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="RemoveAlternativeSecurityIdByIdentityProvider" />
    </OutputClaimsTransformations>
    <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

Profil techniczny przekształcania oświadczeń umożliwia wykonywanie transformacji oświadczeń z dowolnego kroku aranżacji podróży użytkownika. W poniższym przykładzie krok aranżacji wywołuje jeden z niepołączonych profilów technicznych, takich jak **unlink-Facebook-OAuth**. Ten profil techniczny wywołuje **RemoveAlternativeSecurityIdByIdentityProvider**profil techniczny przekształcenia oświadczeń, który generuje nowe oświadczenie **AlternativeSecurityIds2** , które zawiera listę tożsamości społeczności użytkowników, podczas usuwania tożsamości w serwisie Facebook z kolekcji.

```xml
<UserJourney Id="AccountUnLink">
  <OrchestrationSteps>
    ...
    <OrchestrationStep Order="8" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="UnLinkFacebookExchange" TechnicalProfileReferenceId="UnLink-Facebook-OAUTH" />
        <ClaimsExchange Id="UnLinkMicrosoftExchange" TechnicalProfileReferenceId="UnLink-Microsoft-OAUTH" />
        <ClaimsExchange Id="UnLinkGitHubExchange" TechnicalProfileReferenceId="UnLink-GitHub-OAUTH" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ...
  </OrchestrationSteps>
</UserJourney>
```

## <a name="metadata"></a>Metadane

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| IncludeClaimResolvingInClaimsHandling  | Nie | W przypadku oświadczeń wejściowych i wyjściowych określa, czy w profilu technicznym znajduje się [rozpoznawanie oświadczeń](claim-resolver-overview.md) . Możliwe wartości: `true` , lub `false`   (wartość domyślna). Jeśli chcesz użyć programu rozpoznawania oświadczeń w profilu technicznym, ustaw dla tej opcji wartość `true` . |

## <a name="use-a-validation-technical-profile"></a>Użyj profilu technicznego weryfikacji

Profil techniczny transformacji oświadczeń może służyć do weryfikowania informacji. W poniższym przykładzie [profil techniczny](self-asserted-technical-profile.md) z monitem o nazwie **LocalAccountSignUpWithLogonEmail** prosi użytkownika o wprowadzenie adresu e-mail dwa razy, a następnie wywołanie [profilu sprawdzania poprawności](validation-technical-profile.md) w celu weryfikacji **wiadomości e-mail.** Profil techniczny **Validate-email** wywołuje **AssertEmailAreEqual** transformacji oświadczeń w celu porównania dwóch oświadczeń **poczty e-mail** i **emailRepeat**, a następnie zgłasza wyjątek, jeśli nie są one równe zgodnie z określonym porównaniem.

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="AssertEmailAreEqual" TransformationMethod="AssertStringClaimsAreEqual">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim1" />
      <InputClaim ClaimTypeReferenceId="emailRepeat" TransformationClaimType="inputClaim2" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    </InputParameters>
  </ClaimsTransformation>
</ClaimsTransformations>
```

Profil techniczny przekształcania oświadczeń wywołuje transformację oświadczeń **AssertEmailAreEqual** , która potwierdza, że wiadomości e-mail podane przez użytkownika są takie same.

```xml
<TechnicalProfile Id="Validate-Email">
  <DisplayName>Unlink Facebook</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="emailRepeat" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
  </OutputClaims>
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertEmailAreEqual" />
  </OutputClaimsTransformations>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

Własny profil techniczny może wywołać profil techniczny weryfikacji i wyświetlić komunikat o błędzie określony w metadanych **UserMessageIfClaimsTransformationStringsAreNotEqual** .

```xml
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>User ID signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    ...
    <Item Key="UserMessageIfClaimsTransformationStringsAreNotEqual">The email addresses you provided are not the same</Item>
  </Metadata>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
    <OutputClaim ClaimTypeReferenceId="emailRepeat" />
    ...
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="Validate-Email" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```
