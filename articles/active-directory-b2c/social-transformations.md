---
title: Przykłady transformacji oświadczeń kont społecznościowych dla zasad niestandardowych
titleSuffix: Azure AD B2C
description: Przykłady transformacji oświadczeń konta społecznościowego dla schematu programu Identity Experience Framework (IEF) Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c60cecb9d6bfeeefc0569a1a57185d13f0c6442f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94953121"
---
# <a name="social-accounts-claims-transformations"></a>Przekształceń oświadczeń dotyczących kont społecznościowych

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W Azure Active Directory B2C (Azure AD B2C) tożsamości konta społecznościowego są przechowywane w `userIdentities` atrybucie typu " **alternativeSecurityIdCollection** ". Każdy element w **alternativeSecurityIdCollection** określa wystawcy (nazwę dostawcy tożsamości, taki jak Facebook.com) i `issuerUserId` , który jest unikatowym identyfikatorem użytkownika dla wystawcy.

```json
"userIdentities": [{
    "issuer": "google.com",
    "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"
  },
  {
    "issuer": "facebook.com",
    "issuerUserId": "MTIzNDU="
  }]
```

Ten artykuł zawiera przykłady dotyczące korzystania z przekształceń oświadczeń w ramach konta społecznościowego w programie Azure AD B2C. Aby uzyskać więcej informacji, zobacz [ClaimsTransformations](claimstransformations.md).

## <a name="createalternativesecurityid"></a>CreateAlternativeSecurityId

Tworzy reprezentację JSON właściwości alternativeSecurityId użytkownika, która może być używana w wywołaniach do Azure Active Directory. Aby uzyskać więcej informacji, zobacz Schemat [AlternativeSecurityId](/graph/api/resources/alternativesecurityid) .

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | key | ciąg | Wartość oświadczenia, która określa unikatowy identyfikator użytkownika używany przez dostawcę tożsamości społecznościowej. |
| Oświadczenie inputclaim | identityProvider | ciąg | Wartość oświadczenia, która określa nazwę dostawcy tożsamości konta społecznościowego, na przykład facebook.com. |
| Oświadczenie outputclaim | alternativeSecurityId | ciąg | Wartość oświadczenia jest generowana po wywołaniu ClaimsTransformation. Zawiera informacje o tożsamości użytkownika konta społecznościowego. **Wystawcy** jest wartością `identityProvider` żądania. **IssuerUserId** jest wartością `key` żądania w formacie base64. |

Użyj tej transformacji oświadczeń do wygenerowania elementu `alternativeSecurityId` ClaimType. Jest on używany przez wszystkie profile techniczne dostawcy tożsamości społecznościowej, na przykład `Facebook-OAUTH` . Następująca transformacja oświadczeń odbiera identyfikator konta społecznościowego użytkownika i nazwę dostawcy tożsamości. Dane wyjściowe tego profilu technicznego to format ciągu JSON, który może być używany w usługach katalogowych Azure AD.

```xml
<ClaimsTransformation Id="CreateAlternativeSecurityId" TransformationMethod="CreateAlternativeSecurityId">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="issuerUserId" TransformationClaimType="key" />
    <InputClaim ClaimTypeReferenceId="identityProvider" TransformationClaimType="identityProvider" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="alternativeSecurityId" TransformationClaimType="alternativeSecurityId" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczenia wejściowe:
    - **klucz**: 12334
    - **identityProvider**: Facebook.com
- Oświadczenia wyjściowe:
    - **alternativeSecurityId**: {"wystawca": "Facebook.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}

## <a name="additemtoalternativesecurityidcollection"></a>AddItemToAlternativeSecurityIdCollection

Dodaje `AlternativeSecurityId` do `alternativeSecurityIdCollection` żądania.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | element | ciąg | Wartość oświadczenia, która ma zostać dodana do oświadczenia wyjściowego. |
| Oświadczenie inputclaim |  — kolekcja | alternativeSecurityIdCollection | Oświadczenia, które są używane przez transformację oświadczeń, jeśli są dostępne w zasadach. Jeśli ta wartość jest określona, transformacja oświadczeń dodaje na `item` końcu kolekcji. |
| Oświadczenie outputclaim |  — kolekcja | alternativeSecurityIdCollection | Oświadczenia są tworzone po wywołaniu tego ClaimsTransformation. Nowa kolekcja zawierająca elementy z danych wejściowych `collection` i `item` . |

Poniższy przykład łączy nową tożsamość społecznościową z istniejącym kontem. Aby połączyć nową tożsamość społecznościową:
1. W profilach technicznych usługi **AAD-UserReadUsingAlternativeSecurityId** i **AAD-UserReadUsingObjectId** dane wyjściowe żądania **alternativeSecurityIds** użytkownika.
1. Poproszenie użytkownika o zalogowanie się za pomocą jednego z dostawców tożsamości, które nie są skojarzone z tym użytkownikiem.
1. Przy użyciu transformacji oświadczeń **CreateAlternativeSecurityId** Utwórz nowy typ oświadczenia **alternativeSecurityId** o nazwie `AlternativeSecurityId2`
1. Wywołaj transformację oświadczeń **AddItemToAlternativeSecurityIdCollection** , aby dodać oświadczenie **AlternativeSecurityId2** do istniejącego oświadczenia **AlternativeSecurityIds** .
1. Utrwalanie żądania **alternativeSecurityIds** na koncie użytkownika

```xml
<ClaimsTransformation Id="AddAnotherAlternativeSecurityId" TransformationMethod="AddItemToAlternativeSecurityIdCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId2" TransformationClaimType="item" />
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczenia wejściowe:
    - **element**: {"wystawca": "Facebook.com", "issuerUserId": "MTIzNDU ="}
    - **Kolekcja**: [{"wystawca": "Live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}]
- Oświadczenia wyjściowe:
    - **Kolekcja**: [{"wystawca": "Live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}, {"wystawca": "Facebook.com", "issuerUserId": "MTIzNDU ="}]

## <a name="getidentityprovidersfromalternativesecurityidcollectiontransformation"></a>GetIdentityProvidersFromAlternativeSecurityIdCollectionTransformation

Zwraca listę wystawców z **alternativeSecurityIdCollectionego** żądania do nowego żądania **ciągu** .

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | alternativeSecurityIdCollection | alternativeSecurityIdCollection | Wartość oświadczenia, która ma być używana do uzyskiwania listy dostawców tożsamości (wystawca). |
| Oświadczenie outputclaim | identityProvidersCollection | stringCollection | Oświadczenia są tworzone po wywołaniu tego ClaimsTransformation. Lista dostawców tożsamości skojarzona z alternativeSecurityIdCollection wejściowym |

Następująca transformacja oświadczeń odczytuje oświadczenia **alternativeSecurityIds** użytkownika i wyodrębnia listę nazw dostawców tożsamości skojarzonych z tym kontem. Użyj **identityProvidersCollection** wyjściowego, aby wyświetlić użytkownika listę dostawców tożsamości skojarzonych z tym kontem. Na stronie wybór dostawcy tożsamości odfiltruj listę dostawców tożsamości na podstawie danych wyjściowych **identityProvidersCollection** . W takim przypadku użytkownik może wybrać opcję powiązania nowej tożsamości społecznościowej, która nie jest jeszcze skojarzona z kontem.

```xml
<ClaimsTransformation Id="ExtractIdentityProviders" TransformationMethod="GetIdentityProvidersFromAlternativeSecurityIdCollectionTransformation">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityIds" TransformationClaimType="alternativeSecurityIdCollection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="identityProviders" TransformationClaimType="identityProvidersCollection" />
  </OutputClaims>
</ClaimsTransformation>
```

- Oświadczenia wejściowe:
    - **alternativeSecurityIdCollection**: [{"wystawca": "Google.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}, {"wystawca": "Facebook.com", "issuerUserId": "MTIzNDU ="}]
- Oświadczenia wyjściowe:
    - **identityProvidersCollection**: ["Facebook.com", "Google.com"]

## <a name="removealternativesecurityidbyidentityprovider"></a>RemoveAlternativeSecurityIdByIdentityProvider

Usuwa element **AlternativeSecurityId** z **alternativeSecurityIdCollectionego** żądania.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | identityProvider | ciąg | Wartość oświadczenia, która zawiera nazwę dostawcy tożsamości, który ma zostać usunięty z kolekcji. |
| Oświadczenie inputclaim |  — kolekcja | alternativeSecurityIdCollection | Oświadczenia, które są używane przez transformację oświadczeń. Transformacja oświadczeń usuwa identityProvider z kolekcji. |
| Oświadczenie outputclaim |  — kolekcja | alternativeSecurityIdCollection | Oświadczenia są tworzone po wywołaniu tego ClaimsTransformation. Nowa kolekcja po usunięciu identityProvider z kolekcji. |

Poniższy przykład odłącza jedną tożsamość społecznościową z istniejącym kontem. Aby odłączyć tożsamość społecznościową:
1. W profilach technicznych usługi **AAD-UserReadUsingAlternativeSecurityId** i **AAD-UserReadUsingObjectId** dane wyjściowe żądania **alternativeSecurityIds** użytkownika.
2. Poproszenie użytkownika o wybranie konta społecznościowego do usunięcia z listy dostawców tożsamości skojarzonych z tym użytkownikiem.
3. Wywołaj profil techniczny przekształcenia oświadczeń, który wywołuje transformację oświadczeń **RemoveAlternativeSecurityIdByIdentityProvider** , która usunęła wybraną tożsamość społecznościową przy użyciu nazwy dostawcy tożsamości.
4. Utrwalaj **alternativeSecurityIds** na koncie użytkownika.

```xml
<ClaimsTransformation Id="RemoveAlternativeSecurityIdByIdentityProvider" TransformationMethod="RemoveAlternativeSecurityIdByIdentityProvider">
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="secondIdentityProvider" TransformationClaimType="identityProvider" />
        <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
    </InputClaims>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
    </OutputClaims>
</ClaimsTransformation>
</ClaimsTransformations>
```

### <a name="example"></a>Przykład

- Oświadczenia wejściowe:
    - **identityProvider**: Facebook.com
    - **Kolekcja**: [{"wystawca": "Live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}, {"wystawca": "Facebook.com", "issuerUserId": "MTIzNDU ="}]
- Oświadczenia wyjściowe:
    - **Kolekcja**: [{"wystawca": "Live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}]