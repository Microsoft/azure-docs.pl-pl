---
title: Przykłady transformacji oświadczeń ciągów dla zasad niestandardowych
titleSuffix: Azure AD B2C
description: Przykłady transformacji oświadczeń ciągów dla schematu struktury ief (Identity Experience Framework) usługi Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/21/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f08107874598a68fb5ce2a1a8a98b6a81d7b94d4
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81756782"
---
# <a name="string-claims-transformations"></a>Przekształcenia oświadczeń ciągów

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule przedstawiono przykłady użycia przekształceń oświadczeń ciągów schematu struktury środowiska tożsamości w usłudze Azure Active Directory B2C (Azure AD B2C). Aby uzyskać więcej informacji, zobacz [ClaimsTransformations](claimstransformations.md).

## <a name="assertstringclaimsareequal"></a>AssertStringClaimsAreEqual (Twierdzenie siejące)

Porównaj dwa oświadczenia i zgłoś wyjątek, jeśli nie są równe zgodnie z określonym daneem wejściowym porównaniaClaim1, inputClaim2 i stringComparison.

| Element | TransformClaimType (Typ transformacji) | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (własnach wejściowych) | inputClaim1 | ciąg | Typ pierwszego roszczenia, który należy porównać. |
| InputClaim (własnach wejściowych) | inputClaim2 | ciąg | Typ drugiego roszczenia, który należy porównać. |
| Inputparameter | Stringcomparison | ciąg | porównanie ciągów, jedna z wartości: Porządkowy, OrdinalIgnoreCase. |

Transformacja **assertstringclaimsAreEqual** jest zawsze wykonywana z [profilu technicznego sprawdzania poprawności,](validation-technical-profile.md) który jest wywoływany przez [samodzielnie potwierdzony profil techniczny](self-asserted-technical-profile.md)lub [DisplayConrtol](display-controls.md). Metadane `UserMessageIfClaimsTransformationStringsAreNotEqual` samodzielnie potwierdzonego profilu technicznego steruje komunikatem o błędzie, który jest prezentowany użytkownikowi. Komunikaty o błędach mogą być [zlokalizowane](localization-string-ids.md#claims-transformations-error-messages).


![AssertStringClaimsAreEqual wykonanie](./media/string-transformations/assert-execution.png)

Można użyć tej transformacji oświadczeń, aby upewnić się, że dwa ClaimTypes mają taką samą wartość. Jeśli nie, zostanie wyświetlony komunikat o błędzie. Poniższy przykład sprawdza, **czy strongAuthenticationEmailAddress** ClaimType jest równa **e-mail** ClaimType. W przeciwnym razie zostanie wyświetlony komunikat o błędzie.

```XML
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


**Logowanie NonInteractive** sprawdzanie poprawności profilu technicznego wywołuje **AssertEmailAndStrongAuthenticationEmailAddressAreEqual** transformacji oświadczeń.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Samodzielnie potwierdzony profil techniczny wywołuje sprawdzanie poprawności **login-NonInteractive** profil techniczny.

```XML
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
  - **inputClaim1**:someone@contoso.com
  - **inputClaim2**:someone@outlook.com
- Parametry wejściowe:
  - **stringComparison**: ordinalIgnoreCase
- Wynik: Błąd rzucony

## <a name="changecase"></a>Zmiana Pokaz

Zmienia przypadek podanego oświadczenia na małe lub wielkie litery w zależności od operatora.

| Element | TransformClaimType (Typ transformacji) | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (własnach wejściowych) | inputClaim1 | ciąg | Typ oświadczeń, który ma zostać zmieniony. |
| Inputparameter | toCase (toCase) | ciąg | Jedna z następujących `LOWER` wartości: lub `UPPER`. |
| WynikClaim | outputClaim | ciąg | ClaimType, który jest produkowany po tej transformacji oświadczeń został wywołany. |

Użyj tej transformacji oświadczenia, aby zmienić dowolny ciąg ClaimType na małe lub wielkie litery.

```XML
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
  - **adres e-mail**:SomeOne@contoso.com
- Parametry wejściowe:
    - **toCase**: DOLNY
- Oświadczenia wyjściowe:
  - **adres e-mail**:someone@contoso.com

## <a name="createstringclaim"></a>CreateStringClaim (Tworzenie Sznurów)

Tworzy oświadczenie ciągu z podanego parametru wejściowego w transformacji.

| Element | TransformClaimType (Typ transformacji) | Typ danych | Uwagi |
|----- | ----------------------- | --------- | ----- |
| Inputparameter | value | ciąg | Ciąg, który ma zostać ustawiony. Ten parametr wejściowy obsługuje [wyrażenia przekształcania oświadczeń ciągów](string-transformations.md#string-claim-transformations-expressions). |
| WynikClaim | stworzoneClaim | ciąg | ClaimType, który jest produkowany po tej transformacji oświadczeń został wywołany, z wartością określoną w parametr wejściowy. |

Ta transformacja oświadczeń służy do ustawiania wartości typu oświadczenia ciągu.

```XML
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
    - **wartość**: Warunki świadczenia usług contoso...
- Oświadczenia wyjściowe:
    - **createdClaim**: Typ oświadczenia TOS zawiera "Warunki usługi Contoso..." Wartość.

## <a name="compareclaims"></a>PorównajClaims

Określ, czy jedno oświadczenie ciągu jest równe innemu. Rezultatem jest nowy typ roszczenia logicznego `true` `false`o wartości lub .

| Element | TransformClaimType (Typ transformacji) | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (własnach wejściowych) | inputClaim1 | ciąg | Pierwszy typ oświadczenia, który ma być porównywany. |
| InputClaim (własnach wejściowych) | inputClaim2 | ciąg | Drugi typ oświadczenia, który należy porównać. |
| Inputparameter | operator | ciąg | Możliwe `EQUAL` wartości: `NOT EQUAL`lub . |
| Inputparameter | Ignorecase | wartość logiczna | Określa, czy to porównanie powinno ignorować przypadek porównywanych ciągów. |
| WynikClaim | outputClaim | wartość logiczna | ClaimType, który jest produkowany po tej transformacji oświadczeń został wywołany. |

Ta transformacja oświadczeń służy do sprawdzania, czy oświadczenie jest równe innemu oświadczeniu. Na przykład następujące przekształcenie oświadczeń sprawdza, czy wartość oświadczenia **e-mail** jest równa **żądaniu Verified.Email.**

```XML
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
  - **inputClaim1**:someone@contoso.com
  - **inputClaim2**:someone@outlook.com
- Parametry wejściowe:
    - **operator**: NIE RÓWNE
    - **ignoreCase**: prawda
- Oświadczenia wyjściowe:
    - **outputClaim**: prawda

## <a name="compareclaimtovalue"></a>PorównajClaimToValue

Określa, czy wartość oświadczenia jest równa wartości parametru wejściowego.

| Element | TransformClaimType (Typ transformacji) | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (własnach wejściowych) | inputClaim1 | ciąg | Typ oświadczenia, który należy porównać. |
| Inputparameter | operator | ciąg | Możliwe `EQUAL` wartości: `NOT EQUAL`lub . |
| Inputparameter | Compareto | ciąg | porównanie ciągów, jedna z wartości: Porządkowy, OrdinalIgnoreCase. |
| Inputparameter | Ignorecase | wartość logiczna | Określa, czy to porównanie powinno ignorować przypadek porównywanych ciągów. |
| WynikClaim | outputClaim | wartość logiczna | ClaimType, który jest produkowany po tej transformacji oświadczeń został wywołany. |

Transformacja oświadczeń służy do sprawdzania, czy oświadczenie jest równe wartości określonej. Na przykład następujące funkcje przekształcania oświadczeń sprawdza, czy wartość **termsOfUseConsentVersion** oświadczenia jest równa `v1`.

```XML
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
    - **inputClaim1**: v1
- Parametry wejściowe:
    - **compareTo**: V1
    - **operator**: EQUAL
    - **ignoreCase**: prawda
- Oświadczenia wyjściowe:
    - **outputClaim**: prawda

## <a name="createrandomstring"></a>CreateRandomString (Tworzeniesercewanie)

Tworzy losowy ciąg przy użyciu generatora liczb losowych. Jeżeli generator liczb losowych `integer`jest typu, opcjonalnie można podać parametr materiału siewnego i maksymalną liczbę. Opcjonalny parametr formatu ciągu umożliwia formatowanie danych wyjściowych przy użyciu go, a opcjonalny parametr base64 określa, czy dane wyjściowe są zakodowane losowoGeneratorType [guid, integer] outputClaim (String).

| Element | TransformClaimType (Typ transformacji) | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Inputparameter | randomGeneratorType | ciąg | Określa wartość losową, która `GUID` ma zostać wygenerowana `INTEGER` (unikatowy identyfikator globalny) lub (liczba). |
| Inputparameter | Stringformat | ciąg | [Opcjonalnie] Sformatować wartość losową. |
| Inputparameter | base64 | wartość logiczna | [Opcjonalnie] Przekonwertować wartość losową na base64. Jeśli zastosowano format ciągu, wartość po formacie ciągu jest zakodowana do base64. |
| Inputparameter | maksymalna Liczba | int | [Opcjonalnie] Tylko `INTEGER` dla randomGeneratorType. Określ maksymalną liczbę. |
| Inputparameter | Nasion  | int | [Opcjonalnie] Tylko `INTEGER` dla randomGeneratorType. Określ inicjatora dla wartości losowej. Uwaga: ten sam materiał siewny daje taką samą sekwencję liczb losowych. |
| WynikClaim | outputClaim | ciąg | ClaimTypes, które zostaną wyprodukowane po tej transformacji oświadczeń został wywołany. Wartość losowa. |

Poniższy przykład generuje unikatowy identyfikator globalny. Transformacja oświadczeń jest używana do tworzenia losowej nazwy UPN (nazwa zasady użytkownika).

```XML
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
    - **randomGeneratorType**: GUID
- Oświadczenia wyjściowe:
    - **outputClaim**: bc8bedd2-aaa3-411e-bdee-2f1810b73dfc

Poniższy przykład generuje wartość losową liczby całkowitej między 0 i 1000. Wartość jest sformatowana w celu OTP_{losowej wartości}.

```XML
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
    - **randomGeneratorType**: Liczba całkowita
    - **maksymalna Liczba:** 1000
    - **stringFormat**: OTP_{0}
    - **base64**: false
- Oświadczenia wyjściowe:
    - **outputClaim**: OTP_853


## <a name="formatstringclaim"></a>FormatStringClaim

Sformatować oświadczenie zgodnie z podanym ciągiem formatu. Ta transformacja używa `String.Format` metody C#.

| Element | TransformClaimType (Typ transformacji) | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (własnach wejściowych) | inputClaim |ciąg |ClaimType, który działa {0} jako parametr formatu ciągu. |
| Inputparameter | Stringformat | ciąg | Format ciągu, łącznie {0} z parametrem. Ten parametr wejściowy obsługuje [wyrażenia przekształcania oświadczeń ciągów](string-transformations.md#string-claim-transformations-expressions).  |
| WynikClaim | outputClaim | ciąg | ClaimType, który jest produkowany po tej transformacji oświadczeń został wywołany. |

Ta transformacja oświadczeń służy do {0}formatowania dowolnego ciągu za pomocą jednego parametru . Poniższy przykład tworzy **userPrincipalName**. Wszystkie profile techniczne dostawcy tożsamości `Facebook-OAUTH` społecznościowej, takie jak **wywołania CreateUserPrincipalName** do generowania **userPrincipalName**.

```XML
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
    - **inputClaim**: 5164db16-3eee-4629-bfda-dcc3326790e9
- Parametry wejściowe:
    - **stringFormat**: cpim_{0}@{RelyingPartyTenantId}
- Oświadczenia wyjściowe:
  - **outputClaim**:cpim_5164db16-3eee-4629-bfda-dcc3326790e9@b2cdemo.onmicrosoft.com

## <a name="formatstringmultipleclaims"></a>Formatowanie WieluClaims

Formatuj dwa oświadczenia zgodnie z podanym ciągiem formatu. Ta transformacja używa `String.Format` metody C#.

| Element | TransformClaimType (Typ transformacji) | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (własnach wejściowych) | inputClaim |ciąg | ClaimType, który działa {0} jako parametr formatu ciągu. |
| InputClaim (własnach wejściowych) | inputClaim | ciąg | ClaimType, który działa {1} jako parametr formatu ciągu. |
| Inputparameter | Stringformat | ciąg | Format ciągu, w {0} {1} tym i parametry. Ten parametr wejściowy obsługuje [wyrażenia przekształcania oświadczeń ciągów](string-transformations.md#string-claim-transformations-expressions).   |
| WynikClaim | outputClaim | ciąg | ClaimType, który jest produkowany po tej transformacji oświadczeń został wywołany. |

Ta transformacja oświadczeń służy do formatowania dowolnego ciągu z dwoma parametrami {0} i {1}. Poniższy przykład tworzy **displayName** o określonym formacie:

```XML
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
    - **inputClaim1**: Joe
    - **inputClaim2**: Fernando
- Parametry wejściowe:
    - **stringFormat** {0} :{1}
- Oświadczenia wyjściowe:
    - **outputClaim**: Joe Fernando

## <a name="getlocalizedstringstransformation"></a>GetLocalizedStringsTransformacja

Kopiuje zlokalizowane ciągi do oświadczeń.

| Element | TransformClaimType (Typ transformacji) | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| WynikClaim | Nazwa zlokalizowanego ciągu | ciąg | Lista typów oświadczeń, które są produkowane po tej transformacji oświadczeń został wywołany. |

Aby użyć transformacji oświadczeń GetLocalizedStringsTransformation:

1. Zdefiniuj [ciąg lokalizacji](localization.md) i skojarz go z [profilem-technicznym.](self-asserted-technical-profile.md)
1. Element musi być ustawiony `GetLocalizedStringsTransformationClaimType`na . `ElementType` `LocalizedString`
1. Jest `StringId` unikatowy identyfikator, który można zdefiniować i używać go w dalszej części transformacji oświadczeń.
1. W przekształceniu oświadczeń określ listę oświadczeń, które mają być ustawione ze zlokalizowanego ciągu. Jest `ClaimTypeReferenceId` odwołaniem do ClaimType już zdefiniowane w ClaimsSchema sekcji w zasadach. Jest `TransformationClaimType` to nazwa zlokalizowanego ciągu zdefiniowanego `StringId` w `LocalizedString` elemencie.
1. W [samodzielnie potwierdzonym profilu technicznym](self-asserted-technical-profile.md)lub transformacji danych wejściowych lub oświadczeń [danych wyjściowych kontroli wyświetlania](display-controls.md) należy odwołać się do transformacji oświadczeń.

![GetLocalizedStringsTransformacja](./media/string-transformations/get-localized-strings-transformation.png)

W poniższym przykładzie wyszukuje temat wiadomości e-mail, treść, wiadomość kodową i podpis wiadomości e-mail z zlokalizowanych ciągów. Te oświadczenia są później używane przez niestandardowy szablon weryfikacji wiadomości e-mail.

Zdefiniuj zlokalizowane ciągi znaków dla języka angielskiego (domyślnie) i hiszpańskiego.

```XML
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

Transformacja oświadczeń ustawia wartość *podmiotu* typu oświadczenia `StringId` z wartością *email_subject*.

```XML
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
  - **temat**: Kod weryfikacyjny konta Contoso
  - **wiadomość**: Dziękujemy za weryfikację konta!
  - **codeIntro**: Twój kod jest
  - **podpis**: Z poważaniem


## <a name="getmappedvaluefromlocalizedcollection"></a>GetMappedValueFromLocalizedCollection

Wyszukując element z kolekcji **ograniczenia** oświadczenia.

| Element | TransformClaimType (Typ transformacji) | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (własnach wejściowych) | mapFromClaim | ciąg | Oświadczenie, które zawiera tekst, który ma być wyszukany w **restrictionValueClaim** oświadczeń z **Restriction** kolekcji.  |
| WynikClaim | ograniczenieValueClaim | ciąg | Oświadczenie, które zawiera **Restriction** kolekcji. Po wywołaniu transformacji oświadczeń wartość tego oświadczenia zawiera wartość wybranego elementu. |

Poniższy przykład wyszukuje opis komunikatu o błędzie na podstawie klucza błędu. Oświadczenie **responseMsg** zawiera zbiór komunikatów o błędach do przedstawienia użytkownikowi końcowemu lub do wysłania do jednostki uzależniającej.

```XML
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
Transformacja oświadczeń wyszukuje tekst elementu i zwraca jego wartość. Jeśli ograniczenie jest zlokalizowane przy użyciu `<LocalizedCollection>`, transformacja oświadczeń zwraca wartość zlokalizowaną.

```XML
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
    - **restrictionValueClaim**: Nie możesz się zalogować, ponieważ jesteś osobą niepełnoletnią.

## <a name="lookupvalue"></a>WyszukiwanieWejszerość

Wyszukuj wartość oświadczenia z listy wartości na podstawie wartości innego oświadczenia.

| Element | TransformClaimType (Typ transformacji) | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (własnach wejściowych) | inputParameterId (ida danych wejściowych) | ciąg | Oświadczenie zawierające wartość odnośnika |
| Inputparameter | |ciąg | Zbieranie danych wejściowychParametry. |
| Inputparameter | błądOnFailedLookup | wartość logiczna | Kontrolowanie, czy błąd jest zwracany, gdy nie pasujące wyszukiwanie. |
| WynikClaim | inputParameterId (ida danych wejściowych) | ciąg | ClaimTypes, które zostaną wyprodukowane po tej transformacji oświadczeń został wywołany. Wartość pasującego `Id`. |

Poniższy przykład wyszukuje nazwę domeny w jednej z kolekcji inputParameters. Transformacja oświadczeń wyszukuje nazwę domeny w identyfikatorze i zwraca jej wartość (identyfikator aplikacji).

```XML
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
    - **microsoft.com**: 0213308f-17cb-4398-b97e-01da7bd4804e
    - **test.com**: c7026f88-4299-4cdb-965d-3f166464b8a9
    - **errorOnFailedLookup**: false
- Oświadczenia wyjściowe:
    - **outputClaim**: c7026f88-4299-4cdb-965d-3f166464b8a9

Gdy `errorOnFailedLookup` parametr wejściowy `true`jest ustawiony na , transformacja oświadczeń **Odliczeń Jest** zawsze wykonywana z [profilu technicznego sprawdzania poprawności,](validation-technical-profile.md) który jest wywoływany przez samodzielnie [potwierdzony profil techniczny](self-asserted-technical-profile.md)lub [DisplayConrtol](display-controls.md). Metadane `LookupNotFound` samodzielnie potwierdzonego profilu technicznego steruje komunikatem o błędzie, który jest prezentowany użytkownikowi.

![AssertStringClaimsAreEqual wykonanie](./media/string-transformations/assert-execution.png)

Poniższy przykład wyszukuje nazwę domeny w jednej z kolekcji inputParameters. Transformacja oświadczeń wyszukuje nazwę domeny w identyfikatorze i zwraca jej wartość (identyfikator aplikacji) lub wywołuje komunikat o błędzie.

```XML
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
    - **inputParameterId**: live.com
- Parametry wejściowe:
    - **contoso.com**: 13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - **microsoft.com**: 0213308f-17cb-4398-b97e-01da7bd4804e
    - **test.com**: c7026f88-4299-4cdb-965d-3f166464b8a9
    - **errorOnFailedLookup**: prawda
- Błąd:
    - Nie znaleziono dopasowania dla wartości oświadczenia wejściowego na liście identyfikatorów parametrów wejściowych i errorOnFailedLookup jest true.


## <a name="nullclaim"></a>NullClaim (NullClaim)

Oczyść wartość danego oświadczenia.

| Element | TransformClaimType (Typ transformacji) | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| WynikClaim | claim_to_null | ciąg | Wartość oświadczenia jest ustawiona na WARTOŚĆ NULL. |

Użyj tej transformacji oświadczeń, aby usunąć niepotrzebne dane z worka właściwości oświadczeń, aby plik cookie sesji był mniejszy. Poniższy przykład usuwa wartość `TermsOfService` typu oświadczenia.

```XML
<ClaimsTransformation Id="SetTOSToNull" TransformationMethod="NullClaim">
  <OutputClaims>
  <OutputClaim ClaimTypeReferenceId="TermsOfService" TransformationClaimType="claim_to_null" />
  </OutputClaims>
</ClaimsTransformation>
```

- Oświadczenia wejściowe:
    - **outputClaim**: Witamy w aplikacji Contoso. Jeśli nadal przeglądasz i korzystasz z tej strony, zgadzasz się przestrzegać i być związanym z następującymi warunkami...
- Oświadczenia wyjściowe:
    - **outputClaim**: NULL

## <a name="parsedomain"></a>Domena Analiz

Pobiera część domeny adresu e-mail.

| Element | TransformClaimType (Typ transformacji) | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (własnach wejściowych) | Emailaddress | ciąg | ClaimType, który zawiera adres e-mail. |
| WynikClaim | domena | ciąg | ClaimType, który jest produkowany po tej transformacji oświadczeń został wywołany - domeny. |

Ta transformacja oświadczeń służy do analizować nazwę domeny po symbolu @użytkownika. Następująca transformacja oświadczeń pokazuje, jak analizować nazwę domeny z oświadczenia **e-mail.**

```XML
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
  - **adres e-mail:**joe@outlook.com
- Oświadczenia wyjściowe:
    - **domena**: outlook.com

## <a name="setclaimsifregexmatch"></a>SetClaimsIfRegexMatch

Sprawdza, czy `claimToMatch` oświadczenie `matchTo` ciąg i parametr wejściowy są równe i `outputClaimIfMatched` ustawia oświadczenia wyjściowe z wartością obecną `true` w `false` input parametr, wraz z porównania wynik oświadczenia wyjściowego, który ma być ustawiony jako lub na podstawie wyniku porównania.

| Element | TransformClaimType (Typ transformacji) | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | claimToMatch | ciąg | Typ oświadczenia, który ma być porównywany. |
| Inputparameter | dopasowywkiTo | ciąg | Wyrażenie regularne do dopasowania. |
| Inputparameter | outputClaimIfMatched | ciąg | Wartość, która ma być ustawiona, jeśli ciągi są równe. |
| Inputparameter | extractGroups (grupy) | wartość logiczna | [Opcjonalnie] Określa, czy dopasowanie regexu powinno wyodrębnić wartości grup. Możliwe wartości: `true` `false` , lub (domyślnie). | 
| WynikClaim | outputClaim | ciąg | Jeśli wyrażenie regularne jest zgodne, to `outputClaimIfMatched` oświadczenie danych wyjściowych zawiera wartość parametru wejściowego. Lub null, jeśli nie pasuje. |
| WynikClaim | regexCompareResultClaim | wartość logiczna | Typ oświadczenia wynikowego wyniku dopasowania wyrażenia regularnego, `true` `false` który ma być ustawiony jako lub na podstawie wyniku dopasowania. |
| WynikClaim| Nazwa roszczenia| ciąg | Jeśli parametr wejściowy extractGroups ustawiony na true, lista typów oświadczeń, które są tworzone po wywołaniu tego przekształcania oświadczeń. Nazwa claimType musi być zgodna z nazwą grupy Regex. | 

### <a name="example-1"></a>Przykład 1

Sprawdza, czy podany numer telefonu jest prawidłowy, na podstawie wzorca wyrażenia regularnego numeru telefonu.

```XML
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
    - **matchTo**: "^[0-9]{4,16}$"
    - **outputClaimIfMatched**: "isPhone"
- Oświadczenia wyjściowe:
    - **outputClaim**: "isPhone"
    - **regexCompareResultClaim**: prawda

### <a name="example-2"></a>Przykład 2

Sprawdza, czy podany adres e-mail jest prawidłowy, i zwraca alias e-mail.

```XML
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
    - **claimToMatch**:emily@contoso.com"
- Parametry wejściowe:
    - **matchTo**:`(?&lt;mailAlias&gt;.*)@(.*)$`
    - **outputClaimIfMatched**: "isEmail"
    - **extractGroups**: true
- Oświadczenia wyjściowe:
    - **outputClaim**: "isEmail"
    - **regexCompareResultClaim**: prawda
    - **mailAlias**: emily
    
## <a name="setclaimsifstringsareequal"></a>SetClaimsIfStringsAreEqual

Sprawdza, czy oświadczenie `matchTo` ciąg i parametr wejściowy są równe i `stringMatchMsg` `stringMatchMsgCode` ustawia oświadczenia wyjściowe z wartością obecną w `true` `false` i parametrów wejściowych, wraz z porównania oświadczenia wynikowego, który ma być ustawiony jako lub na podstawie wyniku porównania.

| Element | TransformClaimType (Typ transformacji) | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (własnach wejściowych) | inputClaim | ciąg | Typ oświadczenia, który ma być porównywany. |
| Inputparameter | dopasowywkiTo | ciąg | Ciąg, który ma `inputClaim`być porównywany z . |
| Inputparameter | Stringcomparison | ciąg | Możliwe `Ordinal` wartości: `OrdinalIgnoreCase`lub . |
| Inputparameter | ciągMatchMsg | ciąg | Pierwsza wartość do ustawienia, jeśli ciągi są równe. |
| Inputparameter | stringMatchMsgCode | ciąg | Druga wartość do ustawienia, jeśli ciągi są równe. |
| WynikClaim | wyjścieClaim1 | ciąg | Jeśli ciągi są równe, to oświadczenie `stringMatchMsg` danych wyjściowych zawiera wartość parametru wejściowego. |
| WynikClaim | wyjścieClaim2 | ciąg | Jeśli ciągi są równe, to oświadczenie `stringMatchMsgCode` danych wyjściowych zawiera wartość parametru wejściowego. |
| WynikClaim | ciągCompareResultClaim | wartość logiczna | Typ oświadczenia wynik porównania, który ma `true` być `false` ustawiony jako lub na podstawie wyniku porównania. |

Transformacja oświadczeń służy do sprawdzania, czy oświadczenie jest równe wartości określonej. Na przykład następujące funkcje przekształcania oświadczeń sprawdza, czy wartość **termsOfUseConsentVersion** oświadczenia jest równa `v1`. Jeśli tak, zmień `v2`wartość na .

```XML
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
    - **inputClaim**: v1
- Parametry wejściowe:
    - **matchTo**: V1
    - **stringComparison**: ordinalIgnoreCase
    - **stringMatchMsg**: B2C_V1_90005
    - **stringMatchMsgCode**: Tos jest uaktualniany do wersji 2
- Oświadczenia wyjściowe:
    - **outputClaim1**: B2C_V1_90005
    - **outputClaim2**: Tos jest uaktualniany do v2
    - **stringCompareResultClaim**: prawda

## <a name="setclaimsifstringsmatch"></a>SetClaimsIfStringsMatch

Sprawdza, czy oświadczenie `matchTo` ciąg i parametr wejściowy są równe i `outputClaimIfMatched` ustawia oświadczenia wyjściowe z wartością obecną `true` w `false` input parametr, wraz z porównania wynik oświadczenia wyjściowego, który ma być ustawiony jako lub na podstawie wyniku porównania.

| Element | TransformClaimType (Typ transformacji) | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (własnach wejściowych) | claimToMatch | ciąg | Typ oświadczenia, który ma być porównywany. |
| Inputparameter | dopasowywkiTo | ciąg | Ciąg, który ma być porównywany z inputClaim. |
| Inputparameter | Stringcomparison | ciąg | Możliwe `Ordinal` wartości: `OrdinalIgnoreCase`lub . |
| Inputparameter | outputClaimIfMatched | ciąg | Wartość, która ma być ustawiona, jeśli ciągi są równe. |
| WynikClaim | outputClaim | ciąg | Jeśli ciągi są równe, to oświadczenie `outputClaimIfMatched` danych wyjściowych zawiera wartość parametru wejściowego. Lub null, jeśli ciągi nie są zgodne. |
| WynikClaim | ciągCompareResultClaim | wartość logiczna | Typ oświadczenia wynik porównania, który ma `true` być `false` ustawiony jako lub na podstawie wyniku porównania. |

Na przykład następujące funkcje przekształcania oświadczeń sprawdza, czy `Minor`wartość oświadczenia **ageGroup** jest równa . Jeśli tak, zwróć `B2C_V1_90001`wartość do .

```XML
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
    - **claimToMatch**: Drobne
- Parametry wejściowe:
    - **matchTo**: Drobne
    - **stringComparison**: ordinalIgnoreCase
    - **outputClaimIfMatched**: B2C_V1_90001
- Oświadczenia wyjściowe:
    - **isMinorResponseCode**: B2C_V1_90001
    - **isMinor**: prawda


## <a name="stringcontains"></a>CiągiZawiera

Określ, czy określony podciąg występuje w oświadczeniu wejściowym. Rezultatem jest nowy typ roszczenia logicznego `true` `false`o wartości lub . `true`jeśli parametr value występuje w tym `false`ciągu, w przeciwnym razie .

| Element | TransformClaimType (Typ transformacji) | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (własnach wejściowych) | inputClaim | ciąg | Typ oświadczenia, który ma być przeszukiwany. |
|Inputparameter|zawiera|ciąg|Wartość do wyszukania.|
|Inputparameter|Ignorecase|ciąg|Określa, czy to porównanie powinno ignorować przypadek porównywanego ciągu.|
| WynikClaim | outputClaim | ciąg | ClaimType, który jest produkowany po tym ClaimsTransformation został wywołany. Wskaźnik logiczny, jeśli podciąg występuje w oświadczeniu wejściowym. |

Ta transformacja oświadczeń służy do sprawdzania, czy typ oświadczenia ciągu zawiera podciąg. Na przykład sprawdza, `roles` czy typ oświadczenia ciągu zawiera wartość **admina**.

```XML
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
    - **inputClaim**: "Admin, Approver, Editor"
- Parametry wejściowe:
    - **zawiera**: "admin",
    - **ignoreCase**: prawda
- Oświadczenia wyjściowe:
    - **outputClaim**: prawda

## <a name="stringsubstring"></a>Ciągpodciągowy

Wyodrębnia części typu oświadczenia ciągu, zaczynając od znaku w określonym położeniu i zwraca określoną liczbę znaków.

| Element | TransformClaimType (Typ transformacji) | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (własnach wejściowych) | inputClaim | ciąg | Typ oświadczenia, który zawiera ciąg. |
| Inputparameter | Startindex | int | Zerowa pozycja znaku początkowego podciągu w tym wystąpieniu. |
| Inputparameter | length | int | Liczba znaków w podciąg. |
| WynikClaim | outputClaim | wartość logiczna | Ciąg, który jest odpowiednikiem podciąg długości, który rozpoczyna się w startIndex w tym wystąpieniu lub Empty, jeśli startIndex jest równa długości tego wystąpienia i długość wynosi zero. |

Na przykład pobierz prefiks kraju numeru telefonu.


```XML
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
    - **inputClaim**: "+1644114520"
- Parametry wejściowe:
    - **startIndex**: 0
    - **długość**: 2
- Oświadczenia wyjściowe:
    - **outputClaim**: "+1"

## <a name="stringreplace"></a>StringReplace (Miejsce)

Przeszukuje ciąg typu oświadczenia dla określonej wartości i zwraca nowy ciąg typu oświadczenia, w którym wszystkie wystąpienia określonego ciągu w bieżącym ciągu są zastępowane innym określonym ciągiem.

| Element | TransformClaimType (Typ transformacji) | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (własnach wejściowych) | inputClaim | ciąg | Typ oświadczenia, który zawiera ciąg. |
| Inputparameter | Oldvalue | ciąg | Ciąg do przeszukania. |
| Inputparameter | Newvalue | ciąg | Ciąg zastępujący wszystkie wystąpienia`oldValue` |
| WynikClaim | outputClaim | wartość logiczna | Ciąg, który jest odpowiednikiem bieżącego ciągu, z tą różnicą, że wszystkie wystąpienia oldValue są zastępowane newValue. Jeśli oldValue nie zostanie znaleziony w bieżącym wystąpieniu, metoda zwraca bieżące wystąpienie bez zmian. |

Na przykład znormalizować numer telefonu, `-` usuwając znaki


```XML
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
    - **inputClaim**: "+164-411-452-054"
- Parametry wejściowe:
    - **oldValue**: "-"
    - **długość**: ""
- Oświadczenia wyjściowe:
    - **outputClaim**: "+164411452054"

## <a name="stringjoin"></a>StringJoin (łącze smyczkowe)

Łączy elementy określonego typu oświadczenia kolekcji ciągów, używając określonego separatora między każdym elementem lub elementem członkowskim.

| Element | TransformClaimType (Typ transformacji) | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (własnach wejściowych) | inputClaim | Stringcollection | Kolekcja, która zawiera ciągi do łączenia. |
| Inputparameter | ogranicznik | ciąg | Ciąg używany jako separator, taki jak `,`przecinek . |
| WynikClaim | outputClaim | ciąg | Ciąg, który składa się `inputClaim` z elementów członkowskich kolekcji ciągów, rozdzielone przez parametr wejściowy. `delimiter` |

W poniższym przykładzie przyjmuje kolekcję ciągów ról użytkowników i konwertuje go na ciąg ogranicznika przecinka. Ta metoda służy do przechowywania kolekcji ciągów na koncie użytkownika usługi Azure AD. Później podczas odczytywania konta z katalogu, `StringSplit` użyj do konwersji ciągu ogranicznika przecinka z powrotem do kolekcji ciągów.

```XML
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
  - **inputClaim**: [ "Admin", "Autor", "Czytelnik" ]
- Parametry wejściowe:
  - **ogranicznik**: ","
- Oświadczenia wyjściowe:
  - **outputClaim**: "Admin,Autor,Czytelnik"


## <a name="stringsplit"></a>StringSplit (rozsyłanie sznurków

Zwraca tablicę ciągów, która zawiera podciągi w tym wystąpieniu, które są rozdzielane przez elementy określonego ciągu.

| Element | TransformClaimType (Typ transformacji) | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (własnach wejściowych) | inputClaim | ciąg | Typ oświadczenia ciągu, który zawiera ciągi podrzędne do podziału. |
| Inputparameter | ogranicznik | ciąg | Ciąg używany jako separator, taki jak `,`przecinek . |
| WynikClaim | outputClaim | Stringcollection | Kolekcja ciągów, których elementy zawierają podciągi w tym `delimiter` ciągu, które są rozdzielane przez parametr wejściowy. |

W poniższym przykładzie przyjmuje ciąg ogranicznika przecinka ról użytkowników i konwertuje go na kolekcję ciągów.

```XML
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
  - **inputClaim**: "Admin,Autor,Czytelnik"
- Parametry wejściowe:
  - **ogranicznik**: ","
- Oświadczenia wyjściowe:
  - **outputClaim**: [ "Admin", "Autor", "Czytelnik" ]

## <a name="string-claim-transformations-expressions"></a>Wyrażenia przekształceń oświadczeń ciągów
Wyrażenia przekształceń oświadczeń w zasadach niestandardowych usługi Azure AD B2C zawierają informacje kontekstowe dotyczące identyfikatora dzierżawy i identyfikatora profilu technicznego.

  | Wyrażenie | Opis | Przykład |
 | ----- | ----------- | --------|
 | `{TechnicalProfileId}` | Nazwa profilu technicznegoId. | Facebook-OAUTH |
 | `{RelyingPartyTenantId}` | Identyfikator dzierżawy zasad jednostki uzależniającej. | your-tenant.onmicrosoft.com |
 | `{TrustFrameworkTenantId}` | Identyfikator dzierżawy struktury zaufania. | your-tenant.onmicrosoft.com |
