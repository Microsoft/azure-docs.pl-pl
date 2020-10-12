---
title: Lokalizacja — Azure Active Directory B2C
description: Określ element lokalizacji niestandardowych zasad w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 84850b7d44033a2759c51c5c6b9c53d1c945a99d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87005382"
---
# <a name="localization-element"></a>Element lokalizacji

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Element **Lokalizacja** umożliwia obsługę wielu ustawień regionalnych lub języków w zasadach dla podróży użytkownika. Obsługa lokalizacji w ramach zasad pozwala:

- Skonfiguruj jawną listę obsługiwanych języków w zasadach i wybierz język domyślny.
- Podaj odpowiednie dla języka ciągi i kolekcje.

```xml
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
  <LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedResources Id="api.localaccountsignup.es">
  ...
```

Element **lokalizacji** zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Enabled (Włączony) | Nie | Możliwe wartości: `true` lub `false` . |

Element **lokalizacji** zawiera następujące elementy XML

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| SupportedLanguages | 1: n | Lista obsługiwanych języków. |
| LocalizedResources | 0: n | Lista zlokalizowanych zasobów. |

## <a name="supportedlanguages"></a>SupportedLanguages

Element **SupportedLanguages** zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| DefaultLanguage | Tak | Język, który ma być używany jako domyślny dla zlokalizowanych zasobów. |
| MergeBehavior | Nie | Wartości wyliczenia wartości, które są scalone ze wszystkimi oświadczeniami obecnymi w zasadach nadrzędnych o tym samym identyfikatorze. Użyj tego atrybutu podczas zastępowania żądania określonego w zasadach podstawowych. Możliwe wartości: `Append` , `Prepend` , lub `ReplaceAll` . `Append`Wartość określa, że kolekcja danych powinna być dołączana na końcu kolekcji określonej w zasadach nadrzędnych. `Prepend`Wartość określa, że kolekcja danych powinna zostać dodana przed kolekcją określoną w zasadach nadrzędnych. `ReplaceAll`Wartość określa, że kolekcja danych zdefiniowana w zasadach nadrzędnych powinna być ignorowana, przy użyciu zamiast danych zdefiniowanych w bieżących zasadach. |

### <a name="supportedlanguages"></a>SupportedLanguages

Element **SupportedLanguages** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| SupportedLanguage | 1: n | Wyświetla zawartość, która jest zgodna ze znacznikiem języka na RFC 5646-Tagi dla identyfikacji języków. |

## <a name="localizedresources"></a>LocalizedResources

Element **LocalizedResources** zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Id | Tak | Identyfikator, który jest używany do unikatowego identyfikowania zlokalizowanych zasobów. |

Element **LocalizedResources** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| LocalizedCollections | 0: n | Definiuje całe kolekcje w różnych kulturach. Kolekcja może mieć różną liczbę elementów i różne ciągi dla różnych kultur. Przykłady kolekcji obejmują wyliczenia, które pojawiają się w typach zgłoszeń. Na przykład lista krajów/regionów jest pokazywana użytkownikowi na liście rozwijanej. |
| LocalizedStrings | 0: n | Definiuje wszystkie ciągi, z wyjątkiem tych, które znajdują się w kolekcjach w różnych kulturach. |

### <a name="localizedcollections"></a>LocalizedCollections

Element **LocalizedCollections** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Zlokalizowany | 1: n | Lista obsługiwanych języków. |

#### <a name="localizedcollection"></a>Zlokalizowany

**Zlokalizowany** ElementCollection zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| ElementType | Tak | Odwołuje się do elementu ClaimType lub elementu interfejsu użytkownika w pliku zasad. |
| ElementId | Tak | Ciąg, który zawiera odwołanie do typu oświadczenia zdefiniowanego już w sekcji ClaimsSchema, która jest używana, jeśli **ElementType** jest ustawiony jako typ oświadczenia. |
| Targetcollection | Tak | Kolekcja docelowa. |

Element **zlokalizowany** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Element | 0: n | Definiuje dostępną opcję dla użytkownika do wyboru w interfejsie użytkownika, na przykład wartość z listy rozwijanej. |

Element **Item** zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Tekst | Tak | Przyjazny dla użytkownika ciąg wyświetlania, który powinien być widoczny dla użytkownika w interfejsie użytkownika dla tej opcji. |
| Wartość | Tak | Wartość żądania ciągu skojarzona z wybraniem tej opcji. |
| SelectByDefault | Nie | Wskazuje, czy ta opcja powinna być wybrana domyślnie w interfejsie użytkownika. Możliwe wartości: true lub false. |

W poniższym przykładzie pokazano użycie elementu **LocalizedCollections** . Zawiera dwa **zlokalizowane** elementycollection, jeden dla języka angielskiego i drugi dla języka hiszpańskiego. Oba te ustawienia są ustawiane jako kolekcja **ograniczeń** dla żądania `Gender` z listą elementów w języku angielskim i hiszpańskim.

```xml
<LocalizedResources Id="api.selfasserted.en">
 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Female" Value="F" />
      <Item Text="Male" Value="M" />
    </LocalizedCollection>
</LocalizedCollections>

<LocalizedResources Id="api.selfasserted.es">
 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Femenino" Value="F" />
      <Item Text="Masculino" Value="M" />
    </LocalizedCollection>
</LocalizedCollections>
```

### <a name="localizedstrings"></a>LocalizedStrings

Element **LocalizedStrings** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| LocalizedString | 1: n | Zlokalizowany ciąg. |

Element **LocalizedString** zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| ElementType | Tak | Możliwe wartości: [ClaimsProvider](#claimsprovider), [ClaimType](#claimtype), [ErrorMessage](#errormessage), [GetLocalizedStringsTransformationClaimType](#getlocalizedstringstransformationclaimtype), [predykat](#predicate), [InputValidation](#inputvalidation)lub [UxElement](#uxelement).   | 
| ElementId | Tak | Jeśli **ElementType** ma wartość `ClaimType` , `Predicate` , lub `InputValidation` , ten element zawiera odwołanie do typu, który już został zdefiniowany w sekcji ClaimsSchema. |
| StringId | Tak | Jeśli **ElementType** ma wartość `ClaimType` , ten element zawiera odwołanie do atrybutu typu "typ". Możliwe wartości: `DisplayName` , `AdminHelpText` , lub `PatternHelpText` . `DisplayName`Wartość jest używana do ustawiania nazwy wyświetlanej w ramach żądania. `AdminHelpText`Wartość jest używana do ustawiania nazwy tekstu pomocy dla użytkownika. `PatternHelpText`Wartość jest używana do ustawiania tekstu pomocy dla wzorca roszczeń. Jeśli **ElementType** ma wartość `UxElement` , ten element zawiera odwołanie do atrybutu elementu interfejsu użytkownika. Jeśli **ElementType** ma wartość `ErrorMessage` , ten element określa identyfikator komunikatu o błędzie. Aby uzyskać pełną listę identyfikatorów, zobacz [identyfikatory ciągów lokalizacji](localization-string-ids.md) `UxElement` .|

## <a name="elementtype"></a>ElementType

Odwołanie do typu elementu, przekształcenie lub element interfejsu użytkownika w zasadach, które mają być lokalizowane.

| Element do zlokalizowania | ElementType | ElementId |StringId |
| --------- | -------- | ----------- |----------- |
| Nazwa dostawcy tożsamości |`ClaimsProvider`| | Identyfikator elementu ClaimsExchange|
| Atrybuty typu zgłoszenia|`ClaimType`|Nazwa typu zgłoszenia| Atrybut, który ma zostać zlokalizowany. Możliwe wartości: `AdminHelpText` , `DisplayName` , `PatternHelpText` i `UserHelpText` .|
|Komunikat o błędzie|`ErrorMessage`||Identyfikator komunikatu o błędzie |
|Kopiuje zlokalizowane ciągi do oświadczeń|`GetLocalizedStringsTra nsformationClaimType`||Nazwa zgłoszenia wyjściowego|
|Komunikat użytkownika predykatu|`Predicate`|Nazwa predykatu| Atrybut predykatu, który ma zostać zlokalizowany. Możliwe wartości: `HelpText` .|
|Komunikat użytkownika grupy predykatu|`InputValidation`|Identyfikator elementu PredicateValidation.|Identyfikator elementu predykatu. Grupa predykatów musi być elementem podrzędnym elementu walidacji predykatu, jak zdefiniowano w ElementId.|
|Elementy interfejsu użytkownika |`UxElement` | | Identyfikator elementu interfejsu użytkownika, który ma zostać zlokalizowany.|

## <a name="examples"></a>Przykłady

### <a name="claimsprovider"></a>ClaimsProvider

Wartość ClaimsProvider służy do lokalizowania jednej z nazw wyświetlanych dostawców. 

```xml
<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
  </ClaimsExchanges>
</OrchestrationStep>

```

Poniższy przykład pokazuje, jak zlokalizować nazwę wyświetlaną dostawcy roszczeń.

```xml
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">Facebook</LocalizedString>
<LocalizedString ElementType="ClaimsProvider" StringId="GoogleExchange">Google</LocalizedString>
<LocalizedString ElementType="ClaimsProvider" StringId="LinkedInExchange">LinkedIn</LocalizedString>
```

### <a name="claimtype"></a>Claim

Wartość oświadczenia jest używana do lokalizowania jednego z atrybutów oświadczenia. 

```xml
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

Poniższy przykład pokazuje, jak lokalizować atrybuty DisplayName, UserHelpText i PatternHelpText typu zgłoszenia adresu e-mail.

```xml
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
```

### <a name="errormessage"></a>ErrorMessage

Wartość ErrorMessage służy do lokalizowania jednego z komunikatów o błędach systemu. 

```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalAlreadyExists">You are already registered, please press the back button and sign in instead.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

Poniższy przykład pokazuje, jak zlokalizować komunikat o błędzie UserMessageIfClaimsPrincipalAlreadyExists.


```xml
<LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
```

### <a name="getlocalizedstringstransformationclaimtype"></a>GetLocalizedStringsTransformationClaimType

Wartość GetLocalizedStringsTransformationClaimType jest używana do kopiowania zlokalizowanych ciągów do oświadczeń. Aby uzyskać więcej informacji, zobacz [GetLocalizedStringsTransformation — przekształcanie oświadczeń](string-transformations.md#getlocalizedstringstransformation)


```xml
<ClaimsTransformation Id="GetLocalizedStringsForEmail" TransformationMethod="GetLocalizedStringsTransformation">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="subject" TransformationClaimType="email_subject" />
    <OutputClaim ClaimTypeReferenceId="message" TransformationClaimType="email_message" />
    <OutputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="email_code" />
    <OutputClaim ClaimTypeReferenceId="signature" TransformationClaimType="email_signature" />
   </OutputClaims>
</ClaimsTransformation>
```

Poniższy przykład pokazuje, jak lokalizować żądania wyjściowe transformacji oświadczeń GetLocalizedStringsTransformation.

```xml
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for verifying your account!</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
```

### <a name="predicate"></a>Predykatu

Wartość predykatu jest używana do lokalizowania jednego z komunikatów o błędach [predykatu](predicates.md) . 

```xml
<Predicates>
  <Predicate Id="LengthRange" Method="IsLengthRange"  HelpText="The password must be between 6 and 64 characters.">
    <Parameters>
      <Parameter Id="Minimum">6</Parameter>
      <Parameter Id="Maximum">64</Parameter>
    </Parameters>
  </Predicate>
  <Predicate Id="Lowercase" Method="IncludesCharacters" HelpText="a lowercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">a-z</Parameter>
    </Parameters>
  </Predicate>
  <Predicate Id="Uppercase" Method="IncludesCharacters" HelpText="an uppercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">A-Z</Parameter>
    </Parameters>
  </Predicate>
</Predicates>
```

Poniższy przykład przedstawia sposób lokalizowania tekstu pomocy predykatów.

```xml
<LocalizedString ElementType="Predicate" ElementId="LengthRange" StringId="HelpText">The password must be between 6 and 64 characters.</LocalizedString>
<LocalizedString ElementType="Predicate" ElementId="Lowercase" StringId="HelpText">a lowercase letter</LocalizedString>
<LocalizedString ElementType="Predicate" ElementId="Uppercase" StringId="HelpText">an uppercase letter</LocalizedString>
```

### <a name="inputvalidation"></a>InputValidation

Wartość InputValidation służy do lokalizowania jednego z komunikatów o błędach grupy [PredicateValidation](predicates.md) . 

```xml
<PredicateValidations>
  <PredicateValidation Id="CustomPassword">
    <PredicateGroups>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences MatchAtLeast="1">
          <PredicateReference Id="LengthRange" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="CharacterClasses">
        <UserHelpText>The password must have at least 3 of the following:</UserHelpText>
        <PredicateReferences MatchAtLeast="3">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

Poniższy przykład pokazuje, jak zlokalizować tekst pomocy grupy walidacji predykatu.

```xml
<LocalizedString ElementType="InputValidation" ElementId="CustomPassword" StringId="CharacterClasses">The password must have at least 3 of the following:</LocalizedString>
```

### <a name="uxelement"></a>UxElement

Wartość UxElement służy do lokalizowania jednego z elementów interfejsu użytkownika. Poniższy przykład pokazuje, jak zlokalizować przyciski Kontynuuj i Anuluj.

```xml
<LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
<LocalizedString ElementType="UxElement" StringId="button_cancel">Cancel</LocalizedString>
```

## <a name="next-steps"></a>Następne kroki

Przykłady lokalizacji można znaleźć w następujących artykułach:

- [Dostosowywanie języka za pomocą zasad niestandardowych w Azure Active Directory B2C](custom-policy-localization.md)
- [Dostosowywanie języka za pomocą przepływów użytkowników w Azure Active Directory B2C](user-flow-language-customization.md)
