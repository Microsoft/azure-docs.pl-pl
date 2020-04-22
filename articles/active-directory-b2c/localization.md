---
title: Lokalizacja — usługa Azure Active Directory B2C
description: Określ element lokalizacji zasad niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 94ff7ddda41f2df2634d927a7dbf8a5a0d4fc1d8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681414"
---
# <a name="localization"></a>Lokalizacja

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Localization** Element umożliwia obsługę wielu ustawień regionalnych lub języków w zasadach dla podróży użytkownika. Obsługa lokalizacji w zasadach umożliwia:

- Skonfiguruj jawną listę obsługiwanych języków w zasadach i wybierz język domyślny.
- Podaj ciągi i kolekcje specyficzne dla języka.

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
  <LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedResources Id="api.localaccountsignup.es">
  ...
```

Element **Lokalizacja** zawiera następujące atrybuty:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Enabled (Włączony) | Nie | Możliwe `true` wartości: `false`lub . |

Element **Lokalizacja** zawiera następujące elementy XML

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Obsługiwanelanguages | 1:n | Lista obsługiwanych języków. |
| Zlokalizowane Źródła zasobów | 0:n | Lista zlokalizowanych zasobów. |

## <a name="supportedlanguages"></a>Obsługiwanelanguages

Element **SupportedLanguages** zawiera następujące atrybuty:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| DefaultLanguage | Tak | Język, który ma być używany jako domyślny dla zlokalizowanych zasobów. |
| MergeBehavior (Zachowanie scalania) | Nie | Wartości wyliczenia wartości, które są scalane wraz z dowolnego ClaimType obecnych w zasadach nadrzędnych o tym samym identyfikatorze. Użyj tego atrybutu podczas zastępowanie oświadczenia określonego w zasadach podstawowych. Możliwe `Append`wartości: `Prepend`, `ReplaceAll`, lub . Wartość `Append` określa, że kolekcja obecnych danych powinny być dołączane na końcu kolekcji określonych w zasadach nadrzędnych. Wartość `Prepend` określa, że kolekcja obecnych danych powinny zostać dodane przed kolekcji określonych w zasadach nadrzędnych. Wartość `ReplaceAll` określa, że zbieranie danych zdefiniowanych w zasadach nadrzędnych powinny być ignorowane, zamiast tego dane zdefiniowane w bieżącej zasad. |

### <a name="supportedlanguages"></a>Obsługiwanelanguages

Element **SupportedLanguages** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| ObsługiwaneLanguage | 1:n | Wyświetla zawartość, która jest zgodna z tagiem języka na RFC 5646 — tagi dla języków identyfikujących. |

## <a name="localizedresources"></a>Zlokalizowane Źródła zasobów

Element **LocalizedResources** zawiera następujące atrybuty:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Identyfikator | Tak | Identyfikator, który jest używany do jednoznacznej identyfikacji zlokalizowanych zasobów. |

Element **LocalizedResources** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Zlokalizowaneukłaki | 0:n | Definiuje całe kolekcje w różnych kulturach. Kolekcja może mieć różną liczbę elementów i różne ciągi dla różnych kultur. Przykłady kolekcji obejmują wyliczenia, które pojawiają się w typach oświadczeń. Na przykład lista krajów/regionów jest wyświetlana użytkownikowi na liście rozwijanej. |
| Zlokalizowane Sznurki | 0:n | Definiuje wszystkie ciągi, z wyjątkiem tych ciągów, które pojawiają się w kolekcjach, w różnych kulturach. |

### <a name="localizedcollections"></a>Zlokalizowaneukłaki

Element **LocalizedCollections** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Zlokalizowanacollection | 1:n | Lista obsługiwanych języków. |

#### <a name="localizedcollection"></a>Zlokalizowanacollection

Element **LocalizedCollection** zawiera następujące atrybuty:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Elementtype | Tak | Odwołuje się do elementu ClaimType lub elementu interfejsu użytkownika w pliku zasad. |
| ElementId (ład elementu) | Tak | Ciąg, który zawiera odwołanie do typu oświadczenia już zdefiniowane w ClaimsSchema sekcji, która jest używana, jeśli **ElementType** jest ustawiona na ClaimType. |
| CelCollection (CelCollection) | Tak | Kolekcja docelowa. |

Element **LocalizedCollection** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Element | 0:n | Definiuje dostępną opcję dla użytkownika, aby wybrać dla oświadczenia w interfejsie użytkownika, takich jak wartość w rozwijanej. |

**Element element** zawiera następujące atrybuty:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Tekst | Tak | Przyjazny dla użytkownika ciąg wyświetlania, który powinien być wyświetlany użytkownikowi w interfejsie użytkownika dla tej opcji. |
| Wartość | Tak | Wartość oświadczenia ciągu skojarzona z wybraniem tej opcji. |
| Wybierz opcjęObdefault | Nie | Wskazuje, czy ta opcja powinna być zaznaczona domyślnie w interfejsie użytkownika. Możliwe wartości: Prawda lub Fałsz. |

Poniższy przykład pokazuje użycie **LocalizedCollections** elementu. Zawiera dwa **elementy LocalizedCollection,** jeden dla języka angielskiego, a drugi dla języka hiszpańskiego. Oba ustawić **ograniczenie** kolekcji `Gender` roszczenia z listy elementów w języku angielskim i hiszpańskim.

```XML
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

### <a name="localizedstrings"></a>Zlokalizowane Sznurki

Element **LocalizedStrings** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| LocalizedString | 1:n | Zlokalizowany ciąg. |

Element **LocalizedString** zawiera następujące atrybuty:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Elementtype | Tak | Możliwe wartości: [ClaimsProvider](#claimsprovider), [ClaimType](#claimtype), [ErrorMessage](#errormessage), [GetLocalizedStringsTransformationClaimType](#getlocalizedstringstransformationclaimtype), [Predykat,](#predicate) [InputValidation](#inputvalidation)lub [UxElement](#uxelement).   | 
| ElementId (ład elementu) | Tak | Jeśli **ElementType** jest `ClaimType` `Predicate`ustawiona `InputValidation`na , lub , ten element zawiera odwołanie do typu oświadczenia już zdefiniowane w ClaimsSchema sekcji. |
| StringId (ida ciągiem) | Tak | Jeśli **ElementType** jest `ClaimType`ustawiona na , ten element zawiera odwołanie do atrybutu typu oświadczenia. Możliwe `DisplayName`wartości: `AdminHelpText`, `PatternHelpText`, lub . Wartość `DisplayName` jest używana do ustawiania nazwy wyświetlanej oświadczenia. Wartość `AdminHelpText` służy do ustawiania nazwy tekstowej pomocy użytkownika oświadczenia. Wartość `PatternHelpText` służy do ustawiania tekstu pomocy wzorca oświadczeń. Jeśli **ElementType** jest `UxElement`ustawiona na , ten element zawiera odwołanie do atrybutu elementu interfejsu użytkownika. Jeśli **elementType** jest `ErrorMessage`ustawiona na , ten element określa identyfikator komunikatu o błędzie. Zobacz [Identyfikatory ciągów lokalizacji,](localization-string-ids.md) aby `UxElement` uzyskać pełną listę identyfikatorów.|

## <a name="elementtype"></a>Elementtype

ElementType odwołanie do typu oświadczenia, transformacja oświadczenia lub element interfejsu użytkownika w zasadach, które mają być zlokalizowane.

| Element do lokalizacji | Elementtype | ElementId (ład elementu) |StringId (ida ciągiem) |
| --------- | -------- | ----------- |----------- |
| Nazwa dostawcy tożsamości |`ClaimsProvider`| | Identyfikator elementu ClaimsExchange|
| Atrybuty typu oświadczenia|`ClaimType`|Nazwa typu oświadczenia| Atrybut oświadczenia, które mają być zlokalizowane. Możliwe `AdminHelpText`wartości: `DisplayName` `PatternHelpText`, `UserHelpText`, , i .|
|Komunikat o błędzie|`ErrorMessage`||Identyfikator komunikatu o błędzie |
|Kopiuje zlokalizowane ciągi do oświadczeń|`GetLocalizedStringsTra nsformationClaimType`||Nazwa oświadczenia wyjściowego|
|Predykuj komunikat użytkownika|`Predicate`|Nazwa predykatu| Atrybut predykatu, który ma być zlokalizowany. Możliwe wartości: `HelpText`.|
|Predykat wiadomości użytkownika grupy|`InputValidation`|Identyfikator predykatuWalnewłastowanie elementu.|Identyfikator elementu PredicateGroup. Grupa predykatu musi być elementem podrzędnym elementu sprawdzania poprawności predykatu, zgodnie z definicją w ElementId.|
|Elementy interfejsu użytkownika |`UxElement` | | Identyfikator elementu interfejsu użytkownika, który ma być zlokalizowany.|

## <a name="examples"></a>Przykłady

### <a name="claimsprovider"></a>ClaimsProvider

ClaimsProvider wartość jest używana do lokalizowania jednego z dostawców oświadczeń nazwy wyświetlanej. 

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

W poniższym przykładzie pokazano, jak zlokalizować nazwę wyświetlaną dostawców oświadczeń.

```xml
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">Facebook</LocalizedString>
<LocalizedString ElementType="ClaimsProvider" StringId="GoogleExchange">Google</LocalizedString>
<LocalizedString ElementType="ClaimsProvider" StringId="LinkedInExchange">LinkedIn</LocalizedString>
```

### <a name="claimtype"></a>Claimtype

Wartość ClaimType służy do lokalizowania jednego z atrybutów oświadczenia. 

```xml
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

W poniższym przykładzie pokazano, jak zlokalizować DisplayName, UserHelpText i PatternHelpText atrybuty typu oświadczenia wiadomości e-mail.

```XML
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
```

### <a name="errormessage"></a>Errormessage

ErrorMessage Wartość jest używana do lokalizowania jednego z komunikatów o błędach systemu. 

```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalAlreadyExists">You are already registered, please press the back button and sign in instead.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

W poniższym przykładzie pokazano, jak zlokalizować komunikat o błędzie UserMessageIfClaimsPrincipalAlreadyExists.


```XML
<LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
```

### <a name="getlocalizedstringstransformationclaimtype"></a>GetLocalizedStringsTransformationClaimType

GetLocalizedStringsTransformationClaimType wartość jest używana do kopiowania zlokalizowanych ciągów do oświadczeń. Aby uzyskać więcej informacji, zobacz [Transformacja oświadczeń GetLocalizedStringsTransformation](string-transformations.md#getlocalizedstringstransformation)


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

W poniższym przykładzie pokazano, jak zlokalizować oświadczenia wyjściowe transformacji oświadczeń GetLocalizedStringsTransformation.

```xml
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for verifying your account!</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
```

### <a name="predicate"></a>Predykat

Predykat wartość jest używana do lokalizowania jednego z [predykatu](predicates.md) komunikatów o błędach. 

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

W poniższym przykładzie pokazano, jak zlokalizować predykaty tekst pomocy.

```xml
<LocalizedString ElementType="Predicate" ElementId="LengthRange" StringId="HelpText">The password must be between 6 and 64 characters.</LocalizedString>
<LocalizedString ElementType="Predicate" ElementId="Lowercase" StringId="HelpText">a lowercase letter</LocalizedString>
<LocalizedString ElementType="Predicate" ElementId="Uppercase" StringId="HelpText">an uppercase letter</LocalizedString>
```

### <a name="inputvalidation"></a>Wartość danych wejściowych

Wartość InputValidation służy do lokalizowania jednego z komunikatów o błędach grupy [PredicateValidation.](predicates.md) 

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

W poniższym przykładzie pokazano, jak zlokalizować tekst pomocy grupy sprawdzania poprawności predykatu.

```XML
<LocalizedString ElementType="InputValidation" ElementId="CustomPassword" StringId="CharacterClasses">The password must have at least 3 of the following:</LocalizedString>
```

### <a name="uxelement"></a>UxElement ( UxElement )

Wartość UxElement służy do lokalizowania jednego z elementów interfejsu użytkownika. W poniższym przykładzie pokazano, jak zlokalizować przyciski kontynuuj i anuluj.

```XML
<LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
<LocalizedString ElementType="UxElement" StringId="button_cancel">Cancel</LocalizedString>
```

## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły przykładów lokalizacji:

- [Dostosowywanie języka za pomocą zasad niestandardowych w usłudze Azure Active Directory B2C](custom-policy-localization.md)
- [Dostosowywanie języka z przepływami użytkowników w usłudze Azure Active Directory B2C](user-flow-language-customization.md)
