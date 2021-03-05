---
title: Przykłady transformacji oświadczeń ciągów dla zasad niestandardowych
titleSuffix: Azure AD B2C
description: Przykłady transformacji oświadczeń ciągów dla schematu programu Identity Experience Framework (IEF) Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/04/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9cd5a62cd85687767497b142a30d31aa6dd00b77
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175094"
---
# <a name="string-claims-transformations"></a>Przekształcenia oświadczeń ciągów

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule przedstawiono przykłady użycia przekształceń oświadczeń w ramach programu Identity Experience Framework w Azure Active Directory B2C (Azure AD B2C). Aby uzyskać więcej informacji, zobacz [ClaimsTransformations](claimstransformations.md).

## <a name="assertstringclaimsareequal"></a>AssertStringClaimsAreEqual

Porównaj dwa oświadczenia i Zgłoś wyjątek, jeśli nie są one równe zgodnie z określonymi porównaniami inputClaim1, inputClaim2 i stringComparison.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | inputClaim1 | ciąg | Typ pierwszego wystąpienia, który ma zostać porównany. |
| Oświadczenie inputclaim | inputClaim2 | ciąg | Typ drugiego zgłoszenia, który ma zostać porównany. |
| InputParameter | stringComparison | ciąg | porównanie ciągów, jedna z wartości: numer porządkowy, OrdinalIgnoreCase. |

Przekształcenie oświadczeń **AssertStringClaimsAreEqual** jest zawsze wykonywane z poziomu [profilu technicznego weryfikacji](validation-technical-profile.md) , który jest wywoływany przez [profil techniczny z własnym potwierdzeniem](self-asserted-technical-profile.md)lub [DisplayConrtol](display-controls.md). `UserMessageIfClaimsTransformationStringsAreNotEqual`Metadane w profilu technicznym z własnym potwierdzeniem sterują komunikatem o błędzie prezentowanym użytkownikowi. Komunikaty o błędach można [lokalizować](localization-string-ids.md#claims-transformations-error-messages).


![AssertStringClaimsAreEqual wykonywanie](./media/string-transformations/assert-execution.png)

Możesz użyć tej transformacji oświadczeń, aby upewnić się, że dwa oświadczenia muszą mieć tę samą wartość. Jeśli nie, zostanie zgłoszony komunikat o błędzie. Poniższy przykład sprawdza, czy element ClaimType **strongAuthenticationEmailAddress** jest równy elementowi ClaimType **poczty e-mail** . W przeciwnym razie zostanie zgłoszony komunikat o błędzie.

```xml
<ClaimsTransformation Id="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" TransformationMethod="AssertStringClaimsAreEqual">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="strongAuthenticationEmailAddress" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
  </InputParameters>
</ClaimsTransformation>
```


Profil techniczny **nieinteraktywnego sprawdzania poprawności nazwy logowania** jest wywoływany przez **AssertEmailAndStrongAuthenticationEmailAddressAreEqual** transformację oświadczeń.
```xml
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Profil techniczny z własnym potwierdzeniem wywołuje profil techniczny **logowania weryfikacji — nieinteraktywny** .

```xml
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationStringsAreNotEqual">Custom error message the email addresses you provided are not the same.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>Przykład

- Oświadczenia wejściowe:
  - **inputClaim1**: someone@contoso.com
  - **inputClaim2**: someone@outlook.com
- Parametry wejściowe:
  - **stringComparison**: ordinalIgnoreCase
- Wynik: zgłoszono błąd

## <a name="changecase"></a>ChangeCase

Zmienia wielkość liter podanego żądania na niższą lub wielką literę w zależności od operatora.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | inputClaim1 | ciąg | Wartość oświadczenia, która ma zostać zmieniona. |
| InputParameter | toCase | ciąg | Jedna z następujących wartości: `LOWER` lub `UPPER` . |
| Oświadczenie outputclaim | Oświadczenie outputclaim | ciąg | Wartość oświadczenia, która jest generowana po wywołaniu tej transformacji oświadczeń. |

Ta transformacja oświadczenia umożliwia zmianę dowolnego typu oświadczenia ciągu na niski lub wielką literę.

```xml
<ClaimsTransformation Id="ChangeToLower" TransformationMethod="ChangeCase">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim1" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="toCase" DataType="string" Value="LOWER" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczenia wejściowe:
  - **adres e-mail**: SomeOne@contoso.com
- Parametry wejściowe:
    - **toCase**: niższy
- Oświadczenia wyjściowe:
  - **adres e-mail**: someone@contoso.com

## <a name="createstringclaim"></a>CreateStringClaim

Tworzy na podstawie podanego parametru wejściowego wyrażenie typu String.

| Element | TransformationClaimType | Typ danych | Uwagi |
|----- | ----------------------- | --------- | ----- |
| InputParameter | wartość | ciąg | Ciąg, który ma zostać ustawiony. Ten parametr wejściowy obsługuje [wyrażenia transformacji oświadczeń ciągów](string-transformations.md#string-claim-transformations-expressions). |
| Oświadczenie outputclaim | createdClaim | ciąg | Wartość oświadczenia, która jest generowana po wywołaniu tej transformacji oświadczeń, z wartością określoną w parametrze wejściowym. |

Użyj tej transformacji oświadczeń, aby ustawić wartość typu oświadczenia ciągu.

```xml
<ClaimsTransformation Id="CreateTermsOfService" TransformationMethod="CreateStringClaim">
  <InputParameters>
    <InputParameter Id="value" DataType="string" Value="Contoso terms of service..." />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="TOS" TransformationClaimType="createdClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Parametr wejściowy:
    - **wartość**: warunki użytkowania usługi contoso...
- Oświadczenia wyjściowe:
    - **createdClaim**: oświadczenia o organizacjach OT zawiera "warunki korzystania z usługi firmy Contoso..." wartościami.

## <a name="copyclaimifpredicatematch"></a>CopyClaimIfPredicateMatch

Kopiuj wartość żądania do innego, jeśli wartość żądania wejściowego pasuje do predykatu wyniku żądania. 

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | Oświadczenie inputclaim | ciąg | Typ zgłoszenia, który ma zostać skopiowany. |
| Oświadczenie outputclaim | Oświadczenie outputclaim | ciąg | Typ oświadczenia, który jest generowany po wywołaniu tej transformacji oświadczeń. Wartość żądania wejściowego jest sprawdzana względem tego predykatu. |

Poniższy przykład kopiuje wartość "signInName" do żądania denumerowania, tylko jeśli signInName jest numerem telefonu. Aby zapoznać się z kompletnym przykładem, zobacz sekcję [numer telefonu lub logowanie do poczty e-mail](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/Phone_Email_Base.xml) .

```xml
<ClaimsTransformation Id="SetPhoneNumberIfPredicateMatch" TransformationMethod="CopyClaimIfPredicateMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="signInName" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example-1"></a>Przykład 1

- Oświadczenia wejściowe:
    - **oświadczenie inputclaim**: bob@contoso.com
- Oświadczenia wyjściowe:
    - **oświadczenie outputclaim**: nie można zmienić pierwotnej wartości dla zgłoszenia wyjściowego.

### <a name="example-2"></a>Przykład 2

- Oświadczenia wejściowe:
    - **oświadczenie inputclaim**: + 11234567890
- Oświadczenia wyjściowe:
    - **oświadczenie outputclaim**: + 11234567890

## <a name="compareclaims"></a>CompareClaims

Ustal, czy jedno z nich jest równe innemu. Wynik jest nowym obiektem logicznym ClaimType z wartością `true` lub `false` .

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | inputClaim1 | ciąg | Typ pierwszego wystąpienia, który ma zostać porównany. |
| Oświadczenie inputclaim | inputClaim2 | ciąg | Drugi typ zgłoszenia, który ma zostać porównany. |
| InputParameter | operator | ciąg | Możliwe wartości: `EQUAL` lub `NOT EQUAL` . |
| InputParameter | ignoreCase | boolean | Określa, czy to porównanie ma ignorować wielkość liter podczas porównywania ciągów. |
| Oświadczenie outputclaim | Oświadczenie outputclaim | boolean | Wartość oświadczenia, która jest generowana po wywołaniu tej transformacji oświadczeń. |

Ta transformacja oświadczeń służy do sprawdzania, czy oświadczenie jest równe innemu oświadczenia. Na przykład następujące przekształcenia oświadczeń sprawdzają, czy wartość oświadczenia **adresu e-mail** jest równa **Zweryfikowanemu oświadczenia adresu e-mail** .

```xml
<ClaimsTransformation Id="CheckEmail" TransformationMethod="CompareClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="Email" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="Verified.Email" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="operator" DataType="string" Value="NOT EQUAL" />
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="SameEmailAddress" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczenia wejściowe:
  - **inputClaim1**: someone@contoso.com
  - **inputClaim2**: someone@outlook.com
- Parametry wejściowe:
    - **operator**: nie równa się
    - **IgnoreCase**: true
- Oświadczenia wyjściowe:
    - **oświadczenie outputclaim**: true

## <a name="compareclaimtovalue"></a>CompareClaimToValue

Określa, czy wartość żądania jest równa wartości parametru wejściowego.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | inputClaim1 | ciąg | Typ zgłoszenia, który ma zostać porównany. |
| InputParameter | operator | ciąg | Możliwe wartości: `EQUAL` lub `NOT EQUAL` . |
| InputParameter | compareTo | ciąg | porównanie ciągów, jedna z wartości: numer porządkowy, OrdinalIgnoreCase. |
| InputParameter | ignoreCase | boolean | Określa, czy to porównanie ma ignorować wielkość liter podczas porównywania ciągów. |
| Oświadczenie outputclaim | Oświadczenie outputclaim | boolean | Wartość oświadczenia, która jest generowana po wywołaniu tej transformacji oświadczeń. |

Możesz użyć tej transformacji oświadczeń, aby sprawdzić, czy oświadczenie jest równe określonej wartości. Na przykład następujące przekształcenia oświadczeń sprawdzają, czy wartość oświadczenia **termsOfUseConsentVersion** jest równa `v1` .

```xml
<ClaimsTransformation Id="IsTermsOfUseConsentRequiredForVersion" TransformationMethod="CompareClaimToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="inputClaim1" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="compareTo" DataType="string" Value="V1" />
    <InputParameter Id="operator" DataType="string" Value="not equal" />
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład
- Oświadczenia wejściowe:
    - **inputClaim1**: V1
- Parametry wejściowe:
    - **CompareTo**: V1
    - **operator**: równe
    - **IgnoreCase**: true
- Oświadczenia wyjściowe:
    - **oświadczenie outputclaim**: true

## <a name="createrandomstring"></a>CreateRandomString

Tworzy losowy ciąg przy użyciu generatora liczb losowych. Jeśli generator liczb losowych jest typu `integer` , opcjonalnie można podać parametr inicjatora i maksymalną liczbę. Opcjonalny parametr formatu ciągu umożliwia formatowanie danych wyjściowych przy użyciu go, a opcjonalny parametr Base64 określa, czy dane wyjściowe są zakodowane w formacie base64 randomGeneratorType [GUID, Integer] oświadczenie outputclaim (ciąg).

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| InputParameter | randomGeneratorType | ciąg | Określa wartość losową do wygenerowania `GUID` (unikatowy identyfikator globalny) lub `INTEGER` (liczba). |
| InputParameter | StringFormat — | ciąg | Obowiązkowe Sformatuj wartość losową. |
| InputParameter | base64 | boolean | Obowiązkowe Konwertuj wartość losową na format Base64. Jeśli format ciągu jest stosowany, wartość po formacie ciągu jest zaszyfrowana do Base64. |
| InputParameter | maximumNumber | int | Obowiązkowe `INTEGER` Tylko dla randomGeneratorType. Określ maksymalną liczbę. |
| InputParameter | sadzenia  | int | Obowiązkowe `INTEGER` Tylko dla randomGeneratorType. Określ inicjator dla losowej wartości. Uwaga: ten sam inicjator zwraca tę samą sekwencję liczb losowych. |
| Oświadczenie outputclaim | Oświadczenie outputclaim | ciąg | Oświadczenia są tworzone po wywołaniu tej transformacji oświadczeń. Wartość losowa. |

Poniższy przykład generuje globalnie unikatowy identyfikator. Ta transformacja oświadczeń służy do tworzenia losowej nazwy UPN (główna nazwa użytkownika).

```xml
<ClaimsTransformation Id="CreateRandomUPNUserName" TransformationMethod="CreateRandomString">
  <InputParameters>
    <InputParameter Id="randomGeneratorType" DataType="string" Value="GUID" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="upnUserName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Przykład

- Parametry wejściowe:
    - **randomGeneratorType**: identyfikator GUID
- Oświadczenia wyjściowe:
    - **oświadczenie outputclaim**: bc8bedd2-AAA3-411e-bdee-2f1810b73dfc

Poniższy przykład generuje losową wartość całkowitą z zakresu od 0 do 1000. Wartość jest sformatowana do OTP_ {wartość losowa}.

```xml
<ClaimsTransformation Id="SetRandomNumber" TransformationMethod="CreateRandomString">
  <InputParameters>
    <InputParameter Id="randomGeneratorType" DataType="string" Value="INTEGER" />
    <InputParameter Id="maximumNumber" DataType="int" Value="1000" />
    <InputParameter Id="stringFormat" DataType="string" Value="OTP_{0}" />
    <InputParameter Id="base64" DataType="boolean" Value="false" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="randomNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Parametry wejściowe:
    - **randomGeneratorType**: liczba całkowita
    - **maximumNumber**: 1000
    - **StringFormat —**: OTP_{0}
    - **Base64**: false
- Oświadczenia wyjściowe:
    - **oświadczenie outputclaim**: OTP_853


## <a name="formatstringclaim"></a>FormatStringClaim

Sformatuj wierzytelność zgodnie z podanym ciągiem formatu. Ta transformacja używa metody języka C# `String.Format` .

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | Oświadczenie inputclaim |ciąg |Wartość oświadczenia, która działa jako parametr formatu ciągu {0} . |
| InputParameter | StringFormat — | ciąg | Format ciągu, łącznie z {0}  parametrem. Ten parametr wejściowy obsługuje [wyrażenia transformacji oświadczeń ciągów](string-transformations.md#string-claim-transformations-expressions).  |
| Oświadczenie outputclaim | Oświadczenie outputclaim | ciąg | Wartość oświadczenia, która jest generowana po wywołaniu tej transformacji oświadczeń. |

Ta transformacja oświadczeń służy do formatowania dowolnego ciągu z jednym parametrem {0} . Poniższy przykład tworzy element **userPrincipalName**. Wszystkie profile techniczne dostawcy tożsamości społecznościowej, takie jak `Facebook-OAUTH` wywołanie  elementu .

```xml
<ClaimsTransformation Id="CreateUserPrincipalName" TransformationMethod="FormatStringClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="upnUserName" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringFormat" DataType="string" Value="cpim_{0}@{RelyingPartyTenantId}" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczenia wejściowe:
    - **oświadczenie inputclaim**: 5164db16-3eee-4629-bfda-dcc3326790e9
- Parametry wejściowe:
    - **StringFormat —**: cpim_ {0} @ {RelyingPartyTenantId}
- Oświadczenia wyjściowe:
  - **oświadczenie outputclaim**: cpim_5164db16-3eee-4629-bfda-dcc3326790e9@b2cdemo.onmicrosoft.com

## <a name="formatstringmultipleclaims"></a>FormatStringMultipleClaims

Sformatuj dwa oświadczenia zgodnie z podanym ciągiem formatu. Ta transformacja używa metody języka C# `String.Format` .

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | Oświadczenie inputclaim |ciąg | Wartość oświadczenia, która działa jako parametr formatu ciągu {0} . |
| Oświadczenie inputclaim | Oświadczenie inputclaim | ciąg | Wartość oświadczenia, która działa jako parametr formatu ciągu {1} . |
| InputParameter | StringFormat — | ciąg | Format ciągu, łącznie z {0} {1} parametrami i. Ten parametr wejściowy obsługuje [wyrażenia transformacji oświadczeń ciągów](string-transformations.md#string-claim-transformations-expressions).   |
| Oświadczenie outputclaim | Oświadczenie outputclaim | ciąg | Wartość oświadczenia, która jest generowana po wywołaniu tej transformacji oświadczeń. |

Ta transformacja oświadczeń służy do formatowania dowolnego ciągu z dwoma parametrami {0} i {1} . Poniższy przykład tworzy **DisplayName** o określonym formacie:

```xml
<ClaimsTransformation Id="CreateDisplayNameFromFirstNameAndLastName" TransformationMethod="FormatStringMultipleClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="surName" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringFormat" DataType="string" Value="{0} {1}" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="displayName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczenia wejściowe:
    - **inputClaim1**: Jan
    - **inputClaim2**: Fernando
- Parametry wejściowe:
    - **StringFormat —**: {0}{1}
- Oświadczenia wyjściowe:
    - **oświadczenie outputclaim**: Jan Fernando

## <a name="getlocalizedstringstransformation"></a>GetLocalizedStringsTransformation

Kopiuje zlokalizowane ciągi do oświadczeń.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie outputclaim | Nazwa zlokalizowanego ciągu | ciąg | Lista typów oświadczeń, które są tworzone po wywołaniu tej transformacji oświadczeń. |

Aby użyć transformacji oświadczeń GetLocalizedStringsTransformation:

1. Zdefiniuj [ciąg lokalizacji](localization.md) i skojarz go z [profilem niepotwierdzonym przez siebie](self-asserted-technical-profile.md).
1. `ElementType` `LocalizedString` Element musi być ustawiony na `GetLocalizedStringsTransformationClaimType` .
1. `StringId`Jest to unikatowy identyfikator zdefiniowany przez użytkownika i jego użycie później w przekształceniu oświadczeń.
1. W polu transformacja oświadczeń Określ listę oświadczeń, które mają być ustawione przy użyciu zlokalizowanego ciągu. `ClaimTypeReferenceId`Jest odwołaniem do elementu ClaimType zdefiniowanego już w sekcji ClaimsSchema w zasadach. `TransformationClaimType`Jest nazwą zlokalizowanego ciągu zdefiniowanego w `StringId` `LocalizedString` elemencie.
1. W [profilu technicznym z własnym](self-asserted-technical-profile.md)potwierdzeniem lub transformacji danych wejściowych lub wyjściowych [kontrolki wyświetlania](display-controls.md) , Utwórz odwołanie do transformacji oświadczeń.

![GetLocalizedStringsTransformation](./media/string-transformations/get-localized-strings-transformation.png)

W poniższym przykładzie przedstawiono wyszukiwanie tematu wiadomości e-mail, treści, wiadomości z kodem i podpisu wiadomości e-mail z zlokalizowanych ciągów. Te oświadczenia są później używane przez niestandardowy szablon weryfikacji wiadomości e-mail.

Zdefiniuj zlokalizowane ciągi dla języka angielskiego (domyślnego) i hiszpańskiego.

```xml
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="Append">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
   </SupportedLanguages>

  <LocalizedResources Id="api.localaccountsignup.en">
    <LocalizedStrings>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for verifying your account!</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
     </LocalizedStrings>
   </LocalizedResources>
   <LocalizedResources Id="api.localaccountsignup.es">
     <LocalizedStrings>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Código de verificación del correo electrónico de la cuenta de Contoso</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Gracias por comprobar la cuenta de </LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Su código es</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Atentamente</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
</Localization>
```

Transformacja oświadczeń ustawia *wartość typu oświadczenia* , a wartość `StringId` *email_subject*.

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

### <a name="example"></a>Przykład

- Oświadczenia wyjściowe:
  - **podmiot**: kod weryfikacyjny E-mail konta Contoso
  - **komunikat**: Dziękujemy za zweryfikowanie konta.
  - **codeIntro**: Twój kod jest
  - **sygnatura**: z poważaniemi


## <a name="getmappedvaluefromlocalizedcollection"></a>GetMappedValueFromLocalizedCollection

Wyszukiwanie elementu z kolekcji **ograniczeń** dotyczących roszczeń.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | mapFromClaim | ciąg | Oświadczenie zawierające tekst do wyszukania w oświadczeniach **restrictionValueClaim** z kolekcją **ograniczeń** .  |
| Oświadczenie outputclaim | restrictionValueClaim | ciąg | Zastrzeżenie, które zawiera kolekcję **ograniczeń** . Po wywołaniu przekształcenia oświadczeń wartość tego oświadczenia zawiera wartość wybranego elementu. |

Poniższy przykład wyszukuje opis komunikatu o błędzie na podstawie klucza błędu. **ResponseMsg** zawiera kolekcję komunikatów o błędach, które mają być obecne dla użytkownika końcowego lub wysyłane do jednostki uzależnionej.

```xml
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cannot sign in because you are a minor" />
    <Enumeration Text="B2C_V1_90002" Value="This action can only be performed by gold members" />
    <Enumeration Text="B2C_V1_90003" Value="You have not been enabled for this operation" />
  </Restriction>
</ClaimType>
```
Transformacja oświadczeń wyszukuje tekst elementu i zwraca jego wartość. Jeśli ograniczenie jest zlokalizowane przy użyciu `<LocalizedCollection>` , transformacja oświadczeń zwraca zlokalizowaną wartość.

```xml
<ClaimsTransformation Id="GetResponseMsgMappedToResponseCode" TransformationMethod="GetMappedValueFromLocalizedCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="responseCode" TransformationClaimType="mapFromClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="responseMsg" TransformationClaimType="restrictionValueClaim" />        
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczenia wejściowe:
    - **mapFromClaim**: B2C_V1_90001
- Oświadczenia wyjściowe:
    - **restrictionValueClaim**: nie można się zalogować, ponieważ jest to element pomocniczy.

## <a name="lookupvalue"></a>LookupValue

Wyszukaj wartość roszczeń z listy wartości na podstawie wartości innego żądania.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | inputParameterId | ciąg | Zgłoszenie zawierające wartość wyszukiwania |
| InputParameter | |ciąg | Kolekcja inputParameters. |
| InputParameter | errorOnFailedLookup | boolean | Kontrolowanie, czy błąd jest zwracany w przypadku braku zgodnego wyszukiwania. |
| Oświadczenie outputclaim | inputParameterId | ciąg | Oświadczenia są tworzone po wywołaniu tej transformacji oświadczeń. Wartość dopasowywania `Id` . |

Poniższy przykład wyszukuje nazwę domeny w jednej z kolekcji inputParameters. Transformacja oświadczeń wyszukuje nazwę domeny w identyfikatorze i zwraca jej wartość (Identyfikator aplikacji).

```xml
 <ClaimsTransformation Id="DomainToClientId" TransformationMethod="LookupValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="inputParameterId" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="contoso.com" DataType="string" Value="13c15f79-8fb1-4e29-a6c9-be0d36ff19f1" />
    <InputParameter Id="microsoft.com" DataType="string" Value="0213308f-17cb-4398-b97e-01da7bd4804e" />
    <InputParameter Id="test.com" DataType="string" Value="c7026f88-4299-4cdb-965d-3f166464b8a9" />
    <InputParameter Id="errorOnFailedLookup" DataType="boolean" Value="false" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainAppId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczenia wejściowe:
    - **inputParameterId**: test.com
- Parametry wejściowe:
    - **contoso.com**: 13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - **Microsoft.com**: 0213308f-17cb-4398-b97e-01da7bd4804e
    - **test.com**: c7026f88-4299-4CDB-965d-3f166464b8a9
    - **errorOnFailedLookup**: FAŁSZ
- Oświadczenia wyjściowe:
    - **oświadczenie outputclaim**: c7026f88-4299-4CDB-965d-3f166464b8a9

Gdy `errorOnFailedLookup` parametr wejściowy jest ustawiony na `true` , przekształcenie oświadczeń **LookupValue** jest zawsze wykonywane z poziomu [profilu technicznego weryfikacji](validation-technical-profile.md) , który jest wywoływany przez [własny profil techniczny](self-asserted-technical-profile.md)lub [DisplayConrtol](display-controls.md). `LookupNotFound`Metadane w profilu technicznym z własnym potwierdzeniem sterują komunikatem o błędzie prezentowanym użytkownikowi.

![AssertStringClaimsAreEqual wykonywanie](./media/string-transformations/assert-execution.png)

Poniższy przykład wyszukuje nazwę domeny w jednej z kolekcji inputParameters. Transformacja oświadczeń wyszukuje nazwę domeny w identyfikatorze i zwraca jej wartość (Identyfikator aplikacji) lub zgłasza komunikat o błędzie.

```xml
 <ClaimsTransformation Id="DomainToClientId" TransformationMethod="LookupValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="inputParameterId" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="contoso.com" DataType="string" Value="13c15f79-8fb1-4e29-a6c9-be0d36ff19f1" />
    <InputParameter Id="microsoft.com" DataType="string" Value="0213308f-17cb-4398-b97e-01da7bd4804e" />
    <InputParameter Id="test.com" DataType="string" Value="c7026f88-4299-4cdb-965d-3f166464b8a9" />
    <InputParameter Id="errorOnFailedLookup" DataType="boolean" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainAppId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczenia wejściowe:
    - **inputParameterId**: Live.com
- Parametry wejściowe:
    - **contoso.com**: 13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - **Microsoft.com**: 0213308f-17cb-4398-b97e-01da7bd4804e
    - **test.com**: c7026f88-4299-4CDB-965d-3f166464b8a9
    - **errorOnFailedLookup**: true
- Błąd:
    - Nie znaleziono dopasowania dla wartości wejściowego żądania na liście identyfikatorów parametrów wejściowych i errorOnFailedLookup ma wartość true.


## <a name="nullclaim"></a>NullClaim

Wyczyść wartość danego żądania.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie outputclaim | claim_to_null | ciąg | Wartość tego żądania jest ustawiona na wartość NULL. |

Ta transformacja oświadczenia służy do usuwania zbędnych danych z zbioru właściwości oświadczeń, dlatego plik cookie sesji będzie mniejszy. Poniższy przykład usuwa wartość typu "typ" `TermsOfService` .

```xml
<ClaimsTransformation Id="SetTOSToNull" TransformationMethod="NullClaim">
  <OutputClaims>
  <OutputClaim ClaimTypeReferenceId="TermsOfService" TransformationClaimType="claim_to_null" />
  </OutputClaims>
</ClaimsTransformation>
```

- Oświadczenia wejściowe:
    - **oświadczenie outputclaim**: Witamy w aplikacji contoso. Jeśli będziesz kontynuować przeglądanie tej witryny sieci Web i korzystanie z niej, wyrażasz zgodę na przestrzeganie i powiąże się z następującymi warunkami i postanowieniami...
- Oświadczenia wyjściowe:
    - **oświadczenie outputclaim**: null

## <a name="parsedomain"></a>ParseDomain

Pobiera część domeny adresu e-mail.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | emailAddress | ciąg | Wartość oświadczenia, która zawiera adres e-mail. |
| Oświadczenie outputclaim | domena | ciąg | Wartość oświadczenia, która jest generowana po wywołaniu tej transformacji oświadczeń — domena. |

Użyj tej transformacji oświadczeń, aby przeanalizować nazwę domeny po znaku @ symbolu użytkownika. Następująca transformacja oświadczeń pokazuje, jak przeanalizować nazwę domeny z oświadczenia **adresu e-mail** .

```xml
<ClaimsTransformation Id="SetDomainName" TransformationMethod="ParseDomain">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="emailAddress" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="domain" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczenia wejściowe:
  - **EmailAddress**: joe@outlook.com
- Oświadczenia wyjściowe:
    - **domena**: Outlook.com

## <a name="setclaimsifregexmatch"></a>SetClaimsIfRegexMatch

Sprawdza, czy oświadczenie ciągu `claimToMatch` i `matchTo` parametr wejściowy są równe, a następnie ustawia oświadczenia danych wyjściowych o wartości obecne w `outputClaimIfMatched` parametrze wejściowym wraz z oświadczeniem wyjściowym porównania wynik, który ma zostać ustawiony jako `true` lub `false` na podstawie wyniku porównania.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | claimToMatch | ciąg | Typ zgłoszenia, który ma zostać porównany. |
| InputParameter | matchTo | ciąg | Wyrażenie regularne do dopasowania. |
| InputParameter | outputClaimIfMatched | ciąg | Wartość, która ma zostać ustawiona, jeśli ciągi są równe. |
| InputParameter | extractGroups | boolean | Obowiązkowe Określa, czy dopasowanie wyrażenia regularnego powinna wyodrębniać wartości grup. Możliwe wartości: `true` , lub `false` (wartość domyślna). | 
| Oświadczenie outputclaim | Oświadczenie outputclaim | ciąg | Jeśli wyrażenie regularne jest zgodne, to zgłoszenie danych wyjściowych zawiera wartość `outputClaimIfMatched` parametru wejściowego. Lub wartość null, jeśli nie są zgodne. |
| Oświadczenie outputclaim | regexCompareResultClaim | boolean | Wyrażenie regularne dopasowuje typ zgłoszenia wynikowego, który ma zostać ustawiony jako `true` lub `false` w oparciu o wynik dopasowywania. |
| Oświadczenie outputclaim| Nazwa żądania| ciąg | Jeśli parametr wejściowy extractGroups ma wartość true, lista typów oświadczeń, które są tworzone po wywołaniu tej transformacji oświadczeń. Nazwa oświadczenia musi być zgodna z nazwą grupy wyrażeń regularnych. | 

### <a name="example-1"></a>Przykład 1

Sprawdza, czy podany numer telefonu jest prawidłowy, na podstawie wzorca wyrażenia regularnego numeru telefonu.

```xml
<ClaimsTransformation Id="SetIsPhoneRegex" TransformationMethod="SetClaimsIfRegexMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phone" TransformationClaimType="claimToMatch" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="^[0-9]{4,16}$" />
    <InputParameter Id="outputClaimIfMatched" DataType="string" Value="isPhone" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="validationResult" TransformationClaimType="outputClaim" />
    <OutputClaim ClaimTypeReferenceId="isPhoneBoolean" TransformationClaimType="regexCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

- Oświadczenia wejściowe:
    - **claimToMatch**: "64854114520"
- Parametry wejściowe:
    - **matchTo**: "^ [0-9] {4,16} $"
    - **outputClaimIfMatched**: "isphone"
- Oświadczenia wyjściowe:
    - **oświadczenie outputclaim**: "isphone"
    - **regexCompareResultClaim**: true

### <a name="example-2"></a>Przykład 2

Sprawdza, czy podany adres e-mail jest prawidłowy, i zwraca alias adresu e-mail.

```xml
<ClaimsTransformation Id="GetAliasFromEmail" TransformationMethod="SetClaimsIfRegexMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="claimToMatch" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="(?&lt;mailAlias&gt;.*)@(.*)$" />
    <InputParameter Id="outputClaimIfMatched" DataType="string" Value="isEmail" />
    <InputParameter Id="extractGroups" DataType="boolean" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="validationResult" TransformationClaimType="outputClaim" />
    <OutputClaim ClaimTypeReferenceId="isEmailString" TransformationClaimType="regexCompareResultClaim" />
    <OutputClaim ClaimTypeReferenceId="mailAlias" />
  </OutputClaims>
</ClaimsTransformation>
```

- Oświadczenia wejściowe:
    - **claimToMatch**: " emily@contoso.com "
- Parametry wejściowe:
    - **matchTo**: `(?&lt;mailAlias&gt;.*)@(.*)$`
    - **outputClaimIfMatched**: "isemail"
    - **extractGroups**: true
- Oświadczenia wyjściowe:
    - **oświadczenie outputclaim**: "isemail"
    - **regexCompareResultClaim**: true
    - **mailAlias**: Emily
    
## <a name="setclaimsifstringsareequal"></a>SetClaimsIfStringsAreEqual

Sprawdza, czy oświadczenie ciągu i `matchTo` parametr wejściowy są równe, a następnie ustawia oświadczenia danych wyjściowych z wartością obecną w `stringMatchMsg` `stringMatchMsgCode` parametrach i parametrów wejściowych oraz z oświadczeniem wyjściowym porównania wynik, który ma zostać ustawiony jako `true` lub `false` na podstawie wyniku porównania.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | Oświadczenie inputclaim | ciąg | Typ zgłoszenia, który ma zostać porównany. |
| InputParameter | matchTo | ciąg | Ciąg, który ma zostać porównany z `inputClaim` . |
| InputParameter | stringComparison | ciąg | Możliwe wartości: `Ordinal` lub `OrdinalIgnoreCase` . |
| InputParameter | stringMatchMsg | ciąg | Pierwsza wartość, która ma być ustawiona, jeśli ciągi są równe. |
| InputParameter | stringMatchMsgCode | ciąg | Druga wartość, która ma zostać ustawiona, jeśli ciągi są równe. |
| Oświadczenie outputclaim | outputClaim1 | ciąg | Jeśli ciągi są równe, to zgłoszenie wyjściowe zawiera wartość `stringMatchMsg` parametru wejściowego. |
| Oświadczenie outputclaim | outputClaim2 | ciąg | Jeśli ciągi są równe, to zgłoszenie wyjściowe zawiera wartość `stringMatchMsgCode` parametru wejściowego. |
| Oświadczenie outputclaim | stringCompareResultClaim | boolean | Typ zgłoszenia wynikowego porównania, który ma zostać ustawiony jako `true` lub `false` w oparciu o wynik porównania. |

Możesz użyć tej transformacji oświadczeń, aby sprawdzić, czy oświadczenie jest równe określonej wartości. Na przykład następujące przekształcenia oświadczeń sprawdzają, czy wartość oświadczenia **termsOfUseConsentVersion** jest równa `v1` . Jeśli tak, Zmień wartość na `v2` .

```xml
<ClaimsTransformation Id="CheckTheTOS" TransformationMethod="SetClaimsIfStringsAreEqual">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="v1" />
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    <InputParameter Id="stringMatchMsg" DataType="string" Value="B2C_V1_90005" />
    <InputParameter Id="stringMatchMsgCode" DataType="string" Value="The TOS is upgraded to v2" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="outputClaim1" />
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersionUpgradeCode" TransformationClaimType="outputClaim2" />
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersionUpgradeResult" TransformationClaimType="stringCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Przykład

- Oświadczenia wejściowe:
    - **oświadczenie inputclaim**: V1
- Parametry wejściowe:
    - **matchTo**: V1
    - **stringComparison**: ordinalIgnoreCase
    - **stringMatchMsg**: B2C_V1_90005
    - **stringMatchMsgCode**: organizacje telekomunikacyjne są uaktualnione do wersji 2
- Oświadczenia wyjściowe:
    - **outputClaim1**: B2C_V1_90005
    - **outputClaim2**: organizacje telekomunikacyjne są uaktualnione do wersji 2
    - **stringCompareResultClaim**: true

## <a name="setclaimsifstringsmatch"></a>SetClaimsIfStringsMatch

Sprawdza, czy oświadczenie ciągu i `matchTo` parametr wejściowy są równe, a następnie ustawia oświadczenia danych wyjściowych o wartości obecne w `outputClaimIfMatched` parametrze wejściowym wraz z oświadczeniem wyjściowym porównania wynik, który ma zostać ustawiony jako `true` lub `false` na podstawie wyniku porównania.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | claimToMatch | ciąg | Typ zgłoszenia, który ma zostać porównany. |
| InputParameter | matchTo | ciąg | Ciąg, który będzie porównywany z oświadczenie inputclaim. |
| InputParameter | stringComparison | ciąg | Możliwe wartości: `Ordinal` lub `OrdinalIgnoreCase` . |
| InputParameter | outputClaimIfMatched | ciąg | Wartość, która ma zostać ustawiona, jeśli ciągi są równe. |
| Oświadczenie outputclaim | Oświadczenie outputclaim | ciąg | Jeśli ciągi są równe, to zgłoszenie wyjściowe zawiera wartość `outputClaimIfMatched` parametru wejściowego. Lub wartość null, jeśli ciągi nie są zgodne. |
| Oświadczenie outputclaim | stringCompareResultClaim | boolean | Typ zgłoszenia wynikowego porównania, który ma zostać ustawiony jako `true` lub `false` w oparciu o wynik porównania. |

Na przykład następujące przekształcenia oświadczeń sprawdzają, czy wartość oświadczenia **grupy wiekowej** jest równa `Minor` . Jeśli tak, zwróć wartość do `B2C_V1_90001` .

```xml
<ClaimsTransformation Id="SetIsMinor" TransformationMethod="SetClaimsIfStringsMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="ageGroup" TransformationClaimType="claimToMatch" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="Minor" />
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    <InputParameter Id="outputClaimIfMatched" DataType="string" Value="B2C_V1_90001" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isMinor" TransformationClaimType="outputClaim" />
    <OutputClaim ClaimTypeReferenceId="isMinorResponseCode" TransformationClaimType="stringCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczenia wejściowe:
    - **claimToMatch**: pomocniczy
- Parametry wejściowe:
    - **matchTo**: pomocniczy
    - **stringComparison**: ordinalIgnoreCase
    - **outputClaimIfMatched**: B2C_V1_90001
- Oświadczenia wyjściowe:
    - **isMinorResponseCode**: B2C_V1_90001
    - **ismoll**: prawda


## <a name="stringcontains"></a>StringContains

Ustal, czy określony podciąg występuje w ramach żądania wejściowego. Wynik jest nowym obiektem logicznym ClaimType z wartością `true` lub `false` . `true` Jeśli parametr value występuje w tym ciągu, w przeciwnym razie `false` .

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | Oświadczenie inputclaim | ciąg | Typ zgłoszenia, który ma być przeszukiwany. |
|InputParameter|zawiera|ciąg|Wartość do wyszukania.|
|InputParameter|ignoreCase|ciąg|Określa, czy to porównanie ma ignorować wielkość liter podczas porównywania ciągu.|
| Oświadczenie outputclaim | Oświadczenie outputclaim | ciąg | Wartość oświadczenia jest generowana po wywołaniu tego ClaimsTransformation. Wskaźnik wartości logicznej, jeśli podciąg występuje w ramach roszczeń wejściowych. |

Użyj tej transformacji oświadczeń, aby sprawdzić, czy typ oświadczenia ciągu zawiera podciąg. Poniższy przykład sprawdza, czy `roles` Typ zgłoszenia typu zawiera wartość **administrator**.

```xml
<ClaimsTransformation Id="CheckIsAdmin" TransformationMethod="StringContains">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <InputParameters>
    <InputParameter  Id="contains" DataType="string" Value="admin"/>
    <InputParameter  Id="ignoreCase" DataType="string" Value="true"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isAdmin" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczenia wejściowe:
    - **oświadczenie inputclaim**: "admin, osoba zatwierdzająca, Edytor"
- Parametry wejściowe:
    - **zawiera**: "Administrator",
    - **IgnoreCase**: true
- Oświadczenia wyjściowe:
    - **oświadczenie outputclaim**: true

## <a name="stringsubstring"></a>StringSubstring

Wyodrębnia części typu "String", rozpoczynając od znaku w określonej pozycji i zwraca określoną liczbę znaków.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | Oświadczenie inputclaim | ciąg | Typ zgłoszenia, który zawiera ciąg. |
| InputParameter | Indeks | int | Początkowa pozycja znaku w podciągu w tym wystąpieniu (liczony od zera). |
| InputParameter | length | int | Liczba znaków w podciągu. |
| Oświadczenie outputclaim | Oświadczenie outputclaim | boolean | Ciąg, który jest odpowiednikiem podciągu długości, który rozpoczyna się od elementu startIndex w tym wystąpieniu, lub pusty, jeśli wartość startIndex jest równa długości tego wystąpienia, a długość wynosi zero. |

Na przykład Pobierz prefiks kraju/regionu numeru telefonu.


```xml
<ClaimsTransformation Id="GetPhonePrefix" TransformationMethod="StringSubstring">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="inputClaim" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="startIndex" DataType="int" Value="0" />
  <InputParameter Id="length" DataType="int" Value="2" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phonePrefix" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Przykład

- Oświadczenia wejściowe:
    - **oświadczenie inputclaim**: "+ 1644114520"
- Parametry wejściowe:
    - **startIndex**: 0
    - **Długość**: 2
- Oświadczenia wyjściowe:
    - **oświadczenie outputclaim**: "+ 1"

## <a name="stringreplace"></a>StringReplace

Wyszukuje ciąg typu dla określonej wartości i zwraca nowy ciąg typu, w którym wszystkie wystąpienia określonego ciągu w bieżącym ciągu są zamieniane na inny określony ciąg.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | Oświadczenie inputclaim | ciąg | Typ zgłoszenia, który zawiera ciąg. |
| InputParameter | oldValue | ciąg | Ciąg, który ma być przeszukiwany. |
| InputParameter | newValue | ciąg | Ciąg, w którym mają zostać zamienione wszystkie wystąpienia elementu `oldValue` |
| Oświadczenie outputclaim | Oświadczenie outputclaim | boolean | Ciąg, który jest odpowiednikiem bieżącego ciągu, z wyjątkiem tego, że wszystkie wystąpienia wartości oldValue są zastępowane przez newValue. Jeśli w bieżącym wystąpieniu nie znaleziono oldValue, metoda zwraca bieżące wystąpienie bez zmian. |

Na przykład Normalizuj numer telefonu, usuwając `-` znaki


```xml
<ClaimsTransformation Id="NormalizePhoneNumber" TransformationMethod="StringReplace">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="inputClaim" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="oldValue" DataType="string" Value="-" />
  <InputParameter Id="newValue" DataType="string" Value="" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Przykład

- Oświadczenia wejściowe:
    - **oświadczenie inputclaim**: "+ 164-411-452-054"
- Parametry wejściowe:
    - **OldValue**: "-"
    - **NewValue**: ""
- Oświadczenia wyjściowe:
    - **oświadczenie outputclaim**: "+ 164411452054"

## <a name="stringjoin"></a>StringJoin

Łączy elementy określonego typu żądania kolekcji ciągów, używając określonego separatora między każdym elementem lub członkiem.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | Oświadczenie inputclaim | stringCollection | Kolekcja zawierająca ciągi do łączenia. |
| InputParameter | ogranicznik | ciąg | Ciąg, który ma być używany jako separator, taki jak przecinek `,` . |
| Oświadczenie outputclaim | Oświadczenie outputclaim | ciąg | Ciąg składający się z elementów członkowskich `inputClaim` kolekcji ciągów, ograniczony przez `delimiter` parametr wejściowy. |

W poniższym przykładzie jest pobierana kolekcja ról użytkownika i konwertowana na ciąg ogranicznika przecinka. Tej metody można użyć do przechowywania kolekcji ciągów na koncie użytkownika usługi Azure AD. Później, podczas odczytywania konta z katalogu, należy użyć, `StringSplit` Aby przekonwertować ciąg ogranicznika przecinki z powrotem do kolekcji ciągów.

```xml
<ClaimsTransformation Id="ConvertRolesStringCollectionToCommaDelimiterString" TransformationMethod="StringJoin">
  <InputClaims>
   <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter DataType="string" Id="delimiter" Value="," />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="rolesCommaDelimiterConverted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczenia wejściowe:
  - **oświadczenie inputclaim**: ["admin", "author", "Reader"]
- Parametry wejściowe:
  - **ogranicznik**: ","
- Oświadczenia wyjściowe:
  - **oświadczenie outputclaim**: "admin, Author, Reader"


## <a name="stringsplit"></a>StringSplit

Zwraca tablicę ciągów zawierającą podciągi w tym wystąpieniu, które są ograniczone przez elementy określonego ciągu.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | Oświadczenie inputclaim | ciąg | Typ typu "String", który zawiera podciągi do podzielenia. |
| InputParameter | ogranicznik | ciąg | Ciąg, który ma być używany jako separator, taki jak przecinek `,` . |
| Oświadczenie outputclaim | Oświadczenie outputclaim | stringCollection | Kolekcja ciągów, której elementy zawierają podciągi w tym ciągu, które są rozdzielane `delimiter` parametrami wejściowymi. |

W poniższym przykładzie ciąg określający ogranicznik jest rozdzielany przecinkami, a następnie konwertowany jest do kolekcji ciągów.

```xml
<ClaimsTransformation Id="ConvertRolesToStringCollection" TransformationMethod="StringSplit">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="rolesCommaDelimiter" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
  <InputParameter DataType="string" Id="delimiter" Value="," />
    </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="roles" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczenia wejściowe:
  - **oświadczenie inputclaim**: "admin, Author, Reader"
- Parametry wejściowe:
  - **ogranicznik**: ","
- Oświadczenia wyjściowe:
  - **oświadczenie outputclaim**: ["admin", "author", "Reader"]

## <a name="string-claim-transformations-expressions"></a>Wyrażenia przekształceń deklaracji ciągu
Wyrażenia transformacji przekształceń w Azure AD B2C zasad niestandardowych zawierają informacje kontekstu dotyczące identyfikatora dzierżawy i identyfikatora profilu technicznego.

  | Wyrażenie | Opis | Przykład |
 | ----- | ----------- | --------|
 | `{TechnicalProfileId}` | Nazwa profileId techniczna. | Facebook — OAUTH |
 | `{RelyingPartyTenantId}` | Identyfikator dzierżawy zasad jednostki uzależnionej. | your-tenant.onmicrosoft.com |
 | `{TrustFrameworkTenantId}` | Identyfikator dzierżawy struktury zaufania. | your-tenant.onmicrosoft.com |
