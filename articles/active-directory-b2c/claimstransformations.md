---
title: ClaimsTransformations — Azure Active Directory B2C | Microsoft Docs
description: Definicja elementu ClaimsTransformations w schemacie struktury programu Identity Experience Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 37d9bd78a80ac52d2a790537bf47e33807720349
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85202963"
---
# <a name="claimstransformations"></a>ClaimsTransformations

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Element **ClaimsTransformations** zawiera listę funkcji transformacji oświadczeń, które mogą być używane w podróży użytkowników w ramach [zasad niestandardowych](custom-policy-overview.md). Transformacja oświadczeń konwertuje określone oświadczenie na inne. W transformacji oświadczeń należy określić metodę przekształcenia, na przykład dodając element do kolekcji ciągów lub zmieniając wielkość liter w ciągu.

Aby dołączyć listę funkcji przekształcania oświadczeń, które mogą być używane w podróży użytkowników, element XML ClaimsTransformations musi być zadeklarowany w sekcji BuildingBlocks zasad.

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="<identifier>" TransformationMethod="<method>">
    ...
  </ClaimsTransformation>
</ClaimsTransformations>
```

Element **ClaimsTransformation** zawiera następujące atrybuty:

| Atrybut |Wymagane | Opis |
| --------- |-------- | ----------- |
| Id |Tak | Identyfikator, który jest używany do jednoznacznego identyfikowania transformacji roszczeń. Identyfikator jest przywoływany z innych elementów XML w zasadach. |
| TransformationMethod | Tak | Metoda transformacji do użycia w transformacji oświadczeń. Każda transformacja roszczeń ma swoje własne wartości. Aby uzyskać pełną listę dostępnych wartości, zobacz [Dokumentacja transformacji oświadczeń](#claims-transformations-reference) . |

## <a name="claimstransformation"></a>ClaimsTransformation

Element **ClaimsTransformation** zawiera następujące elementy:

```xml
<ClaimsTransformation Id="<identifier>" TransformationMethod="<method>">
  <InputClaims>
    ...
  </InputClaims>
  <InputParameters>
    ...
  </InputParameters>
  <OutputClaims>
    ...
  </OutputClaims>
</ClaimsTransformation>
```


| Element | Wystąpień | Opis |
| ------- | -------- | ----------- |
| InputClaims | 0:1 | Lista elementów **oświadczenie inputclaim** , które określają typy oświadczeń, które są pobierane jako dane wejściowe do transformacji oświadczeń. Każdy z tych elementów zawiera odwołanie do elementu ClaimType zdefiniowanego już w sekcji ClaimsSchema w zasadach. |
| InputParameters | 0:1 | Lista elementów **InputParameter** , które są dostarczane jako dane wejściowe do przekształcania oświadczeń.
| OutputClaims | 0:1 | Lista elementów **oświadczenie outputclaim** , które określają typy roszczeń, które są tworzone po wywołaniu ClaimsTransformation. Każdy z tych elementów zawiera odwołanie do elementu ClaimType zdefiniowanego już w sekcji ClaimsSchema. |

### <a name="inputclaims"></a>InputClaims

Element **InputClaims** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Oświadczenie inputclaim | 1: n | Oczekiwany typ zgłoszenia wejściowego. |

#### <a name="inputclaim"></a>Oświadczenie inputclaim

Element **oświadczenie inputclaim** zawiera następujące atrybuty:

| Atrybut |Wymagane | Opis |
| --------- | ----------- | ----------- |
| ClaimTypeReferenceId |Tak | Odwołanie do elementu ClaimType zdefiniowanego już w sekcji ClaimsSchema w zasadach. |
| TransformationClaimType |Tak | Identyfikator, który odwołuje się do typu przywoływanego elementu transformacji. Każda transformacja roszczeń ma swoje własne wartości. Aby uzyskać pełną listę dostępnych wartości, zobacz [Dokumentacja transformacji oświadczeń](#claims-transformations-reference) . |

### <a name="inputparameters"></a>InputParameters

Element **InputParameters** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| InputParameter | 1: n | Oczekiwano parametru wejściowego. |

#### <a name="inputparameter"></a>InputParameter

| Atrybut | Wymagane |Opis |
| --------- | ----------- |----------- |
| Id | Tak | Identyfikator, który jest odwołaniem do parametru metody przekształcania oświadczeń. Każda metoda transformacji oświadczeń ma swoje własne wartości. Zobacz tabelę transformacji oświadczeń, aby uzyskać pełną listę dostępnych wartości. |
| typ_danych | Tak | Typ danych parametru, taki jak String, Boolean, int lub DateTime jako na Wyliczenie typu danych w schemacie XML zasad niestandardowych. Ten typ jest używany do poprawnego wykonywania operacji arytmetycznych. Każda transformacja roszczeń ma swoje własne wartości. Aby uzyskać pełną listę dostępnych wartości, zobacz [Dokumentacja transformacji oświadczeń](#claims-transformations-reference) . |
| Wartość | Tak | Wartość, która jest przenoszona Verbatim do transformacji. Niektóre wartości są dowolne, ale niektóre z nich wybiera się z metody przekształcania oświadczeń. |

### <a name="outputclaims"></a>OutputClaims

Element **OutputClaims** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Oświadczenie outputclaim | 0: n | Oczekiwany typ zgłoszenia wyjściowego. |

#### <a name="outputclaim"></a>Oświadczenie outputclaim

Element **oświadczenie outputclaim** zawiera następujące atrybuty:

| Atrybut |Wymagane | Opis |
| --------- | ----------- |----------- |
| ClaimTypeReferenceId | Tak | Odwołanie do elementu ClaimType zdefiniowanego już w sekcji ClaimsSchema w zasadach.
| TransformationClaimType | Tak | Identyfikator, który odwołuje się do typu przywoływanego elementu transformacji. Każda transformacja roszczeń ma swoje własne wartości. Aby uzyskać pełną listę dostępnych wartości, zobacz [Dokumentacja transformacji oświadczeń](#claims-transformations-reference) . |

Jeśli dane wejściowe i dane wyjściowe są tego samego typu (ciąg lub wartość logiczna), można użyć tego samego żądania wejściowego jako żądania danych wyjściowych. W takim przypadku transformacja oświadczeń zmienia oświadczenie wejściowe na wartość wyjściową.

## <a name="example"></a>Przykład

Można na przykład przechowywać w ostatniej wersji usługi, która zaakceptowała użytkownika. Po zaktualizowaniu warunków użytkowania można polecić użytkownikowi zaakceptowanie nowej wersji. W poniższym przykładzie transformacja oświadczeń **HasTOSVersionChanged** porównuje wartość oświadczenia **TOSVersion** z wartością oświadczenia **LastTOSAcceptedVersion** , a następnie zwraca oświadczenie **TOSVersionChanged** logicznego.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="TOSVersionChanged">
      <DisplayName>Indicates if the TOS version accepted by the end user is equal to the current version</DisplayName>
      <DataType>boolean</DataType>
    </ClaimType>
    <ClaimType Id="TOSVersion">
      <DisplayName>TOS version</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    <ClaimType Id="LastTOSAcceptedVersion">
      <DisplayName>TOS version accepted by the end user</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <ClaimsTransformation Id="HasTOSVersionChanged" TransformationMethod="CompareClaims">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="TOSVersion" TransformationClaimType="inputClaim1" />
        <InputClaim ClaimTypeReferenceId="LastTOSAcceptedVersion" TransformationClaimType="inputClaim2" />
      </InputClaims>
      <InputParameters>
        <InputParameter Id="operator" DataType="string" Value="NOT EQUAL" />
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="TOSVersionChanged" TransformationClaimType="outputClaim" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

## <a name="claims-transformations-reference"></a>Dokumentacja transformacji oświadczeń

Przykłady przekształceń oświadczeń można znaleźć na następujących stronach odniesienia:

- [Boolean](boolean-transformations.md)
- [Data](date-transformations.md)
- [Liczba całkowita](integer-transformations.md)
- [JSON](json-transformations.md)
- [Numer telefonu](phone-number-claims-transformations.md)
- [Ogólne](general-transformations.md)
- [Konto społecznościowe](social-transformations.md)
- [Ciąg](string-transformations.md)
- [StringCollection](stringcollection-transformations.md)

