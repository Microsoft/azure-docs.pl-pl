---
title: Przykłady logicznej transformacji oświadczeń dla zasad niestandardowych
titleSuffix: Azure AD B2C
description: Przykłady logicznej transformacji oświadczeń dla schematu programu Identity Experience Framework (IEF) Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/06/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7c292f939339add06168c55236f8666651e4aace
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85201280"
---
# <a name="boolean-claims-transformations"></a>Przekształcenia logiczne oświadczeń

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule przedstawiono przykłady użycia przekształceń logicznej oświadczeń schematu programu Identity Experience Framework w Azure Active Directory B2C (Azure AD B2C). Aby uzyskać więcej informacji, zobacz [ClaimsTransformations](claimstransformations.md).

## <a name="andclaims"></a>Wnoszonych

Wykonuje operację i dwie inputClaims logiczne i ustawia oświadczenie outputclaim z wynikiem operacji.

| Element  | TransformationClaimType  | Typ danych  | Uwagi |
|-------| ------------------------ | ---------- | ----- |
| Oświadczenie inputclaim | inputClaim1 | boolean | Pierwszy ClaimType do obliczenia. |
| Oświadczenie inputclaim | inputClaim2  | boolean | Drugie wyrażenie Claim, które ma zostać obliczone. |
|Oświadczenie outputclaim | Oświadczenie outputclaim | boolean | Oświadczenia są tworzone po wywołaniu tej transformacji oświadczeń (true lub false). |

Następująca transformacja oświadczeń pokazuje, jak i dwa elementy Claims Boolean: `isEmailNotExist` , i `isSocialAccount` . Oświadczenie wyjściowe `presentEmailSelfAsserted` ma ustawioną `true` wartość, jeśli wartością obu oświadczeń wejściowych są `true` . W kroku aranżacji możesz użyć warunku wstępnego, aby wstępnie zapewnieł stronę, tylko jeśli adres e-mail konta społecznościowego jest pusty.

```xml
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="AndClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isEmailNotExist" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="isSocialAccount" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="presentEmailSelfAsserted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example-of-andclaims"></a>Przykład wnoszonych

- Oświadczenia wejściowe:
    - **inputClaim1**: true
    - **inputClaim2**: FAŁSZ
- Oświadczenia wyjściowe:
    - **oświadczenie outputclaim**: FAŁSZ


## <a name="assertbooleanclaimisequaltovalue"></a>AssertBooleanClaimIsEqualToValue

Sprawdza, czy wartości logiczne dwóch oświadczeń są równe, i zgłasza wyjątek, jeśli nie.

| Element | TransformationClaimType  | Typ danych  | Uwagi |
| ---- | ------------------------ | ---------- | ----- |
| Oświadczenie inputclaim | Oświadczenie inputclaim | boolean | Wartość oświadczenia, która ma zostać potwierdzona. |
| InputParameter |valueToCompareTo | boolean | Wartość do porównania (true lub false). |

Przekształcenie oświadczeń **AssertBooleanClaimIsEqualToValue** jest zawsze wykonywane z poziomu [weryfikacji profilu technicznego](validation-technical-profile.md) , który jest wywoływany przez [własny profil techniczny](self-asserted-technical-profile.md). **UserMessageIfClaimsTransformationBooleanValueIsNotEqual** z własnym profilem technicznym metadane są kontrolowane komunikat o błędzie, który zostanie wyświetlony przez profil techniczny. Komunikaty o błędach można [lokalizować](localization-string-ids.md#claims-transformations-error-messages).

![AssertStringClaimsAreEqual wykonywanie](./media/boolean-transformations/assert-execution.png)

Następująca transformacja oświadczeń pokazuje, jak sprawdzić wartość logicznego elementu ClaimType z `true` wartością. Jeśli wartość `accountEnabled` oświadczenia jest równa false, zostanie zgłoszony komunikat o błędzie.

```xml
<ClaimsTransformation Id="AssertAccountEnabledIsTrue" TransformationMethod="AssertBooleanClaimIsEqualToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="accountEnabled" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="valueToCompareTo" DataType="boolean" Value="true" />
  </InputParameters>
</ClaimsTransformation>
```


`login-NonInteractive`Profil techniczny weryfikacji wywołuje `AssertAccountEnabledIsTrue` transformację oświadczeń.

```xml
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertAccountEnabledIsTrue" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Profil techniczny z własnym potwierdzeniem wywołuje profil techniczny **logowania weryfikacji — nieinteraktywny** .

```xml
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationBooleanValueIsNotEqual">Custom error message if account is disabled.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example-of-assertbooleanclaimisequaltovalue"></a>Przykład AssertBooleanClaimIsEqualToValue

- Oświadczenia wejściowe:
    - **oświadczenie inputclaim**: FAŁSZ
    - **valueToCompareTo**: true
- Wynik: zgłoszono błąd

## <a name="comparebooleanclaimtovalue"></a>CompareBooleanClaimToValue

Sprawdza, czy wartość logiczna żądania jest równa `true` lub `false` i zwraca wynik kompresji.

| Element | TransformationClaimType  | Typ danych  | Uwagi |
| ---- | ------------------------ | ---------- | ----- |
| Oświadczenie inputclaim | Oświadczenie inputclaim | boolean | Wartość oświadczenia, która ma zostać potwierdzona. |
| InputParameter |valueToCompareTo | boolean | Wartość do porównania (true lub false). |
| Oświadczenie outputclaim | compareResult | boolean | Wartość oświadczenia jest generowana po wywołaniu tego ClaimsTransformation. |

Następująca transformacja oświadczeń pokazuje, jak sprawdzić wartość logicznego elementu ClaimType z `true` wartością. Jeśli wartość `IsAgeOver21Years` oświadczenia jest równa `true` , transformacja oświadczeń zwraca `true` , w przeciwnym razie `false` .

```xml
<ClaimsTransformation Id="AssertAccountEnabled" TransformationMethod="CompareBooleanClaimToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="IsAgeOver21Years" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="valueToCompareTo" DataType="boolean" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim  ClaimTypeReferenceId="accountEnabled" TransformationClaimType="compareResult"/>
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example-of-comparebooleanclaimtovalue"></a>Przykład CompareBooleanClaimToValue

- Oświadczenia wejściowe:
    - **oświadczenie inputclaim**: FAŁSZ
- Parametry wejściowe:
    - **valueToCompareTo**: true
- Oświadczenia wyjściowe:
    - **compareResult**: FAŁSZ

## <a name="notclaims"></a>NotClaims

Wykonuje operację not dla oświadczenie inputclaim logicznego i ustawia oświadczenie outputclaim z wynikiem operacji.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | Oświadczenie inputclaim | boolean | Zastrzeżenie, które ma być obsługiwane. |
| Oświadczenie outputclaim | Oświadczenie outputclaim | boolean | Oświadczenia są tworzone po wywołaniu tego ClaimsTransformation (true lub false). |

Ta transformacja jest używana do wykonywania logicznego negacji dla żądania.

```xml
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="NotClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example-of-notclaims"></a>Przykład NotClaims

- Oświadczenia wejściowe:
    - **oświadczenie inputclaim**: FAŁSZ
- Oświadczenia wyjściowe:
    - **oświadczenie outputclaim**: true

## <a name="orclaims"></a>OrClaims

Oblicza wartość lub z dwóch inputClaims logicznych i ustawia oświadczenie outputclaim z wynikiem operacji.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | inputClaim1 | boolean | Pierwszy ClaimType do obliczenia. |
| Oświadczenie inputclaim | inputClaim2 | boolean | Drugie wyrażenie Claim, które ma zostać obliczone. |
| Oświadczenie outputclaim | Oświadczenie outputclaim | boolean | Oświadczenia są tworzone po wywołaniu tego ClaimsTransformation (true lub false). |

Następująca transformacja oświadczeń pokazuje, jak `Or` dwa elementy Claims Boolean. W kroku aranżacji można użyć warunku wstępnego, aby wstępnie zatwierdzić stronę, jeśli wartość jednego z oświadczeń jest równa `true` .

```xml
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="OrClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedNotExists" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedGreaterThanNow" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="presentTOSSelfAsserted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example-of-orclaims"></a>Przykład OrClaims

- Oświadczenia wejściowe:
    - **inputClaim1**: true
    - **inputClaim2**: FAŁSZ
- Oświadczenia wyjściowe:
    - **oświadczenie outputclaim**: true
