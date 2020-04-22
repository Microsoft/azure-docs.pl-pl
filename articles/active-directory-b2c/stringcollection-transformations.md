---
title: StringCollection żąda przykładów transformacji dla zasad niestandardowych
titleSuffix: Azure AD B2C
description: StringCollection żąda przykładów transformacji dla schematu struktury środowiska tożsamości (IEF) usługi Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/21/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cac7e6feb632456b63b97ead057f9ecaf49322ea
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729716"
---
# <a name="stringcollection-claims-transformations"></a>StringCollection roszczeń przekształceń

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule przedstawiono przykłady użycia przekształceń oświadczeń kolekcji ciągów schematu struktury środowiska tożsamości w usłudze Azure Active Directory B2C (Azure AD B2C). Aby uzyskać więcej informacji, zobacz [ClaimsTransformations](claimstransformations.md).

## <a name="additemtostringcollection"></a>AddItemToStringKollection

Dodaje oświadczenie ciągu do nowego unikatowego żądania stringCollection.

| Element | TransformClaimType (Typ transformacji) | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (własnach wejściowych) | element | ciąg | ClaimType, które mają zostać dodane do oświadczenia wyjściowego. |
| InputClaim (własnach wejściowych) |  — kolekcja | Stringcollection | [Opcjonalnie] Jeśli określono, transformacja oświadczeń kopiuje elementy z tej kolekcji i dodaje element na końcu oświadczenia kolekcji danych wyjściowych. |
| WynikClaim |  — kolekcja | Stringcollection | ClaimType, który jest produkowany po tej transformacji oświadczeń został wywołany, z wartością określoną w oświadczeń wejściowych. |

Ta transformacja oświadczeń służy do dodawania ciągu do nowego lub istniejącego stringCollection. Jest powszechnie używany w profilu technicznym **AAD-UserWriteUsingAlternativeSecurityId.** Przed utworzeniem nowego konta **społecznościowego, CreateOtherMailsFromEmail** roszczeń transformacja odczytuje ClaimType i dodaje wartość do **otherMails** ClaimType.

Następująca transformacja oświadczeń dodaje typ oświadczenia **e-mail** do **otherMails** ClaimType.

```XML
<ClaimsTransformation Id="CreateOtherMailsFromEmail" TransformationMethod="AddItemToStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="item" />
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczenia wejściowe:
  - **kolekcja**:someone@outlook.com[" "]
  - **pozycja**:admin@contoso.com" "
- Oświadczenia wyjściowe:
  - **kolekcja**:someone@outlook.com["admin@contoso.com", " "]

## <a name="addparametertostringcollection"></a>AddParameterToStringCollection

Dodaje parametr string do nowego unikatowego żądania stringCollection.

| Element | TransformClaimType (Typ transformacji) | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (własnach wejściowych) |  — kolekcja | Stringcollection | [Opcjonalnie] Jeśli określono, transformacja oświadczeń kopiuje elementy z tej kolekcji i dodaje element na końcu oświadczenia kolekcji danych wyjściowych. |
| Inputparameter | element | ciąg | Wartość dodana do oświadczenia wyjściowego. |
| WynikClaim |  — kolekcja | Stringcollection | ClaimType, który jest produkowany po tej transformacji oświadczeń został wywołany, z wartością określoną w parametr wejściowy. |

Ta transformacja oświadczeń służy do dodawania wartości ciągu do nowego lub istniejącego stringCollection. Poniższy przykład dodaje stałyadmin@contoso.comadres e-mail ( ) do **oświadczenia otherMails.**

```XML
<ClaimsTransformation Id="SetCompanyEmail" TransformationMethod="AddParameterToStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="item" DataType="string" Value="admin@contoso.com" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczenia wejściowe:
  - **kolekcja**:someone@outlook.com[" "]
- Parametry wejściowe
  - **pozycja**:admin@contoso.com" "
- Oświadczenia wyjściowe:
  - **kolekcja**:someone@outlook.com["admin@contoso.com", " "]

## <a name="getsingleitemfromstringcollection"></a>GetSingleItemFromStringKolekcja

Pobiera pierwszy element z dostarczonej kolekcji ciągów.

| Element | TransformClaimType (Typ transformacji) | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (własnach wejściowych) |  — kolekcja | Stringcollection | ClaimTypes, które są używane przez transformację oświadczeń, aby uzyskać element. |
| WynikClaim | wyodrębnioneJedItem | ciąg | ClaimTypes, które są produkowane po tym ClaimsTransformation został wywołany. Pierwszy element w kolekcji. |

W poniższym przykładzie odczytuje **oświadczenie otherMails** i zwraca pierwszy element do oświadczenia **e-mail.**

```XML
<ClaimsTransformation Id="CreateEmailFromOtherMails" TransformationMethod="GetSingleItemFromStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="extractedItem" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczenia wejściowe:
  - **kolekcja**:someone@outlook.com["someone@contoso.com", " "]
- Oświadczenia wyjściowe:
  - **extractedItem**:someone@outlook.com" "


## <a name="stringcollectioncontains"></a>Zawieranie znaków

Sprawdza, czy typ oświadczenia StringCollection zawiera element

| Element | TransformClaimType (Typ transformacji) | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (własnach wejściowych) | inputClaim | Stringcollection | Typ oświadczenia, który ma być przeszukiwany. |
|Inputparameter|element|ciąg|Wartość do wyszukania.|
|Inputparameter|Ignorecase|ciąg|Określa, czy to porównanie powinno ignorować przypadek porównywanych ciągów.|
| WynikClaim | outputClaim | wartość logiczna | ClaimType, który jest produkowany po tym ClaimsTransformation został wywołany. Wskaźnik logiczny, jeśli kolekcja zawiera taki ciąg |

Poniższy przykład `roles` sprawdza, czy typ oświadczenia stringCollection zawiera wartość **admina**.

```XML
<ClaimsTransformation Id="IsAdmin" TransformationMethod="StringCollectionContains">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <InputParameters>
    <InputParameter  Id="item" DataType="string" Value="Admin"/>
    <InputParameter  Id="ignoreCase" DataType="string" Value="true"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isAdmin" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

- Oświadczenia wejściowe:
    - **inputClaim**: ["reader", "autor", "admin"]
- Parametry wejściowe:
    - **pozycja**: "Admin"
    - **ignoreCase**: "true"
- Oświadczenia wyjściowe:
    - **outputClaim**: "true"

## <a name="stringcollectioncontainsclaim"></a>StringCollectionContainsClaim

Sprawdza, czy typ oświadczenia StringCollection zawiera wartość oświadczenia.

| Element | TransformClaimType (Typ transformacji) | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (własnach wejściowych) |  — kolekcja | Stringcollection | Typ oświadczenia, który ma być przeszukiwany. |
| InputClaim (własnach wejściowych) | element|ciąg| Typ oświadczenia zawierający wartość do wyszukania.|
|Inputparameter|Ignorecase|ciąg|Określa, czy to porównanie powinno ignorować przypadek porównywanych ciągów.|
| WynikClaim | outputClaim | wartość logiczna | ClaimType, który jest produkowany po tym ClaimsTransformation został wywołany. Wskaźnik logiczny, jeśli kolekcja zawiera taki ciąg |

Poniższy przykład `roles` sprawdza, czy stringCollection typ `role` oświadczenia zawiera wartość typu oświadczenia.

```XML
<ClaimsTransformation Id="HasRequiredRole" TransformationMethod="StringCollectionContainsClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="collection" />
    <InputClaim ClaimTypeReferenceId="role" TransformationClaimType="item" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="hasAccess" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation> 
```

- Oświadczenia wejściowe:
    - **kolekcja**: ["reader", "author", "admin"]
    - **pozycja**: "Admin"
- Parametry wejściowe:
    - **ignoreCase**: "true"
- Oświadczenia wyjściowe:
    - **outputClaim**: "true"
