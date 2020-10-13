---
title: Profile techniczne usługi Azure AD SSPR w zasadach niestandardowych
titleSuffix: Azure AD B2C
description: Niestandardowe informacje dotyczące zasad profilów technicznych usługi Azure AD SSPR w programie Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/23/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3e6fcf956639d827a8654c5ee80e7cab8cadf930
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85383601"
---
# <a name="define-an-azure-ad-sspr-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Zdefiniuj profil techniczny usługi Azure AD SSPR w zasadach niestandardowych Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) zapewnia obsługę weryfikowania adresu e-mail do samoobsługowego resetowania hasła (SSPR). Użyj profilu technicznego usługi Azure AD SSPR, aby wygenerować i wysłać kod na adres e-mail, a następnie sprawdź kod. Profil techniczny usługi Azure AD SSPR może również zwrócić komunikat o błędzie. Profil techniczny weryfikacji weryfikuje dane dostarczone przez użytkownika przed kontynuowaniem podróży użytkownika. Po sprawdzeniu profilu technicznego na stronie z potwierdzeniem zostanie wyświetlony komunikat o błędzie.

Ten profil techniczny:

- Nie zapewnia interfejsu do współdziałania z użytkownikiem. Zamiast tego interfejs użytkownika jest wywoływany z profilu technicznego z [własnym potwierdzeniem](self-asserted-technical-profile.md) lub [kontrolki wyświetlania](display-controls.md) jako [profil techniczny weryfikacji](validation-technical-profile.md).
- Program używa usługi Azure AD SSPR do generowania i wysyłania kodu na adres e-mail, a następnie weryfikuje kod.  
- Sprawdza poprawność adresu e-mail za pomocą kodu weryfikacyjnego.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Protokół

Atrybut **name** elementu **Protocol** musi być ustawiony na `Proprietary` . Atrybut **programu obsługi** musi zawierać w pełni kwalifikowaną nazwę zestawu programu obsługi protokołu, który jest używany przez Azure AD B2C:

```
Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

Poniższy przykład przedstawia profil techniczny usługi Azure AD SSPR:

```XML
<TechnicalProfile Id="AadSspr-SendCode">
  <DisplayName>Send Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    ...
```

## <a name="send-email"></a>Wyślij wiadomość e-mail

Pierwszy tryb tego profilu technicznego polega na wygenerowaniu kodu i wysłaniu go. Dla tego trybu można skonfigurować następujące opcje.

### <a name="input-claims"></a>Oświadczenia wejściowe

Element **InputClaims** zawiera listę oświadczeń do wysłania do usługi Azure AD SSPR. Możesz również zmapować nazwę swojego zgłoszenia na nazwę zdefiniowaną w profilu technicznym SSPR.

| ClaimReferenceId | Wymagane | Opis |
| --------- | -------- | ----------- |
| emailAddress | Tak | Identyfikator użytkownika, który jest właścicielem adresu e-mail. `PartnerClaimType`Właściwość żądania wejściowego musi być ustawiona na wartość `emailAddress` . |


Element **InputClaimsTransformations** może zawierać kolekcję elementów **InputClaimsTransformation** , które są używane do modyfikowania oświadczeń wejściowych lub generować nowe przed wysłaniem do usługi Azure AD SSPR.

### <a name="output-claims"></a>Oświadczenia wyjściowe

Dostawca protokołu usługi Azure AD SSPR nie zwraca żadnych **OutputClaims**, więc nie ma potrzeby określania oświadczeń wyjściowych. Można jednak uwzględnić oświadczenia, które nie są zwracane przez dostawcę protokołu usługi Azure AD SSPR, tak długo, jak ustawisz `DefaultValue` atrybut.

Element **OutputClaimsTransformations** może zawierać kolekcję elementów **OutputClaimsTransformation** , które są używane do modyfikowania oświadczeń wyjściowych lub generowania nowych.

### <a name="metadata"></a>Metadane

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Operacja | Tak | Musi być **kontrolka sendcode**.  |

#### <a name="ui-elements"></a>Elementy interfejsu użytkownika

Poniższe metadane mogą służyć do konfigurowania komunikatów o błędach wyświetlanych podczas wysyłania błędu SMS. Metadane należy skonfigurować w profilu technicznym z [własnym potwierdzeniem](self-asserted-technical-profile.md) . Komunikaty o błędach można [lokalizować](localization-string-ids.md#azure-ad-sspr).

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| UserMessageIfInternalError | Nie | Komunikat o błędzie użytkownika, jeśli serwer napotkał błąd wewnętrzny. |
| UserMessageIfThrottled| Nie | Komunikat o błędzie użytkownika, jeśli żądanie zostało ograniczone.|


### <a name="example-send-an-email"></a>Przykład: Wyślij wiadomość e-mail

Poniższy przykład przedstawia profil techniczny usługi Azure AD SSPR, który jest używany do wysyłania kodu za pośrednictwem poczty e-mail.

```XML
<TechnicalProfile Id="AadSspr-SendCode">
  <DisplayName>Send Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">SendCode</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress"/>
  </InputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>Zweryfikuj kod

Drugi tryb tego profilu technicznego polega na sprawdzeniu kodu. Dla tego trybu można skonfigurować następujące opcje.

### <a name="input-claims"></a>Oświadczenia wejściowe

Element **InputClaims** zawiera listę oświadczeń do wysłania do usługi Azure AD SSPR. Możesz również zmapować nazwę swojego zgłoszenia na nazwę zdefiniowaną w profilu technicznym SSPR.

| ClaimReferenceId | Wymagane | Opis |
| --------- | -------- | ----------- | ----------- |
| emailAddress| Tak | Ten sam adres e-mail, który został wcześniej użyty do wysłania kodu. Służy również do lokalizowania sesji weryfikacyjnej poczty e-mail. `PartnerClaimType`Właściwość żądania wejściowego musi być ustawiona na wartość `emailAddress` .|
| verificationCode  | Tak | Kod weryfikacyjny dostarczony przez użytkownika do zweryfikowania. `PartnerClaimType`Właściwość żądania wejściowego musi być ustawiona na wartość `verificationCode` . |

Element **InputClaimsTransformations** może zawierać kolekcję elementów **InputClaimsTransformation** , które są używane do modyfikowania oświadczeń wejściowych lub generować nowe przed WYWOŁANIEM usługi Azure AD SSPR.

### <a name="output-claims"></a>Oświadczenia wyjściowe

Dostawca protokołu usługi Azure AD SSPR nie zwraca żadnych **OutputClaims**, więc nie ma potrzeby określania oświadczeń wyjściowych. Można jednak uwzględnić oświadczenia, które nie są zwracane przez dostawcę protokołu usługi Azure AD SSPR, tak długo, jak ustawisz `DefaultValue` atrybut.

Element **OutputClaimsTransformations** może zawierać kolekcję elementów **OutputClaimsTransformation** , które są używane do modyfikowania oświadczeń wyjściowych lub generowania nowych.

### <a name="metadata"></a>Metadane

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Operacja | Tak | Musi być **kontrolka verifycode** |

#### <a name="ui-elements"></a>Elementy interfejsu użytkownika

Poniższe metadane mogą służyć do konfigurowania komunikatów o błędach wyświetlanych podczas sprawdzania kodu. Metadane należy skonfigurować w profilu technicznym z [własnym potwierdzeniem](self-asserted-technical-profile.md) . Komunikaty o błędach można [lokalizować](localization-string-ids.md#azure-ad-sspr).

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
|UserMessageIfChallengeExpired | Komunikat, który ma być wyświetlany użytkownikowi, jeśli sesja weryfikacji kodu wygasła. Kod wygasł lub kod nigdy nie został wygenerowany dla danego identyfikatora.|
|UserMessageIfInternalError | Komunikat o błędzie użytkownika, jeśli serwer napotkał błąd wewnętrzny.|
|UserMessageIfThrottled | Komunikat o błędzie użytkownika, jeśli żądanie zostało ograniczone.|
|UserMessageIfVerificationFailedNoRetry | Komunikat, który ma być wyświetlany użytkownikowi w przypadku podania nieprawidłowego kodu, a użytkownik nie może podać poprawnego kodu.|
|UserMessageIfVerificationFailedRetryAllowed | Komunikat, który ma być wyświetlany użytkownikowi w przypadku podania nieprawidłowego kodu, a użytkownik może podać poprawny kod.|

### <a name="example-verify-a-code"></a>Przykład: Weryfikowanie kodu

Poniższy przykład przedstawia profil techniczny usługi Azure AD SSPR służący do sprawdzania kodu.

```XML
<TechnicalProfile Id="AadSspr-VerifyCode">
  <DisplayName>Verify Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">VerifyCode</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="verificationCode" PartnerClaimType="verificationCode" />
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress"/>
  </InputClaims>
</TechnicalProfile>
```