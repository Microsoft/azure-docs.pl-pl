---
title: Profile techniczne usługi Azure AD MFA w zasadach niestandardowych
titleSuffix: Azure AD B2C
description: Niestandardowe informacje o zasadach dla profilów technicznych usługi Azure AD Multi-Factor Authentication (MFA) w Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e81ac35555e6653cecb602e5af2f19aa3e2f05e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94840597"
---
# <a name="define-an-azure-ad-mfa-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Zdefiniuj profil techniczny usługi Azure AD MFA w zasadach niestandardowych Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Usługa Azure Active Directory B2C (Azure AD B2C) zapewnia obsługę weryfikowania numeru telefonu przy użyciu usługi Azure AD Multi-Factor Authentication (MFA). Użyj tego profilu technicznego, aby wygenerować i wysłać kod na numer telefonu, a następnie zweryfikować ten kod. Profil techniczny usługi Azure AD MFA może również zwrócić komunikat o błędzie.  Profil techniczny weryfikacji weryfikuje dane dostarczone przez użytkownika przed kontynuowaniem podróży użytkownika. Po sprawdzeniu profilu technicznego na stronie z potwierdzeniem zostanie wyświetlony komunikat o błędzie.

Ten profil techniczny:

- Nie zapewnia interfejsu do współdziałania z użytkownikiem. Zamiast tego interfejs użytkownika jest wywoływany z profilu technicznego z [własnym potwierdzeniem](self-asserted-technical-profile.md) lub [kontrolki wyświetlania](display-controls.md) jako [profil techniczny weryfikacji](validation-technical-profile.md).
- Program używa usługi Azure AD MFA w celu wygenerowania i wysłania kodu do numeru telefonu, a następnie weryfikuje kod.  
- Sprawdza poprawność numeru telefonu za pośrednictwem wiadomości SMS.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Protokół

Atrybut **name** elementu **Protocol** musi być ustawiony na `Proprietary` . Atrybut **programu obsługi** musi zawierać w pełni kwalifikowaną nazwę zestawu programu obsługi protokołu, który jest używany przez Azure AD B2C:

```
Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

Poniższy przykład przedstawia profil techniczny usługi Azure AD MFA:

```xml
<TechnicalProfile Id="AzureMfa-SendSms">
    <DisplayName>Send Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    ...
```

## <a name="send-sms"></a>Wyślij wiadomość SMS

Pierwszy tryb tego profilu technicznego polega na wygenerowaniu kodu i wysłaniu go. Dla tego trybu można skonfigurować następujące opcje.

### <a name="input-claims"></a>Oświadczenia wejściowe

Element **InputClaims** zawiera listę oświadczeń do wysłania do usługi Azure AD MFA. Możesz również zmapować nazwę swojego zgłoszenia na nazwę zdefiniowaną w profilu technicznym usługi MFA.

| ClaimReferenceId | Wymagane | Opis |
| --------- | -------- | ----------- |
| userPrincipalName | Tak | Identyfikator użytkownika, który jest właścicielem numeru telefonu. |
| phoneNumber | Tak | Numer telefonu, na który ma zostać wysłany kod SMS. |
| companyName | Nie |Nazwa firmy w wiadomości SMS. Jeśli nie zostanie podany, zostanie użyta nazwa aplikacji. |
| locale | Nie | Ustawienia regionalne wiadomości SMS. Jeśli nie zostanie podany, używane są ustawienia regionalne użytkownika w przeglądarce. |

Element **InputClaimsTransformations** może zawierać kolekcję elementów **InputClaimsTransformation** , które są używane do modyfikowania oświadczeń wejściowych lub generować nowe przed wysłaniem do usługi Azure AD MFA.

### <a name="output-claims"></a>Oświadczenia wyjściowe

Dostawca protokołu usługi Azure AD MFA nie zwraca żadnych **OutputClaims**, więc nie ma potrzeby określania oświadczeń wyjściowych. Można jednak uwzględnić oświadczenia, które nie są zwracane przez dostawcę tożsamości usługi Azure AD MFA, tak długo, jak ustawisz `DefaultValue` atrybut.

Element **OutputClaimsTransformations** może zawierać kolekcję elementów **OutputClaimsTransformation** , które są używane do modyfikowania oświadczeń wyjściowych lub generowania nowych.

### <a name="metadata"></a>Metadane

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Operacja | Tak | Musi być **OneWaySMS**.  |

#### <a name="ui-elements"></a>Elementy interfejsu użytkownika

Poniższe metadane mogą służyć do konfigurowania komunikatów o błędach wyświetlanych podczas wysyłania błędu SMS. Metadane należy skonfigurować w profilu technicznym z [własnym potwierdzeniem](self-asserted-technical-profile.md) . Komunikaty o błędach można [lokalizować](localization-string-ids.md#azure-ad-mfa-error-messages).

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| UserMessageIfCouldntSendSms | Nie | Komunikat o błędzie użytkownika, jeśli podany numer telefonu nie akceptuje wiadomości SMS. |
| UserMessageIfInvalidFormat | Nie | Komunikat o błędzie użytkownika, jeśli podany numer telefonu nie jest prawidłowym numerem telefonu. |
| UserMessageIfServerError | Nie | Komunikat o błędzie użytkownika, jeśli serwer napotkał błąd wewnętrzny. |
| UserMessageIfThrottled| Nie | Komunikat o błędzie użytkownika, jeśli żądanie zostało ograniczone.|

### <a name="example-send-an-sms"></a>Przykład: Wyślij wiadomość SMS

Poniższy przykład przedstawia profil techniczny usługi Azure AD MFA, który jest używany do wysyłania kodu za pośrednictwem wiadomości SMS.

```xml
<TechnicalProfile Id="AzureMfa-SendSms">
  <DisplayName>Send Sms</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">OneWaySMS</Item>
  </Metadata>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CombinePhoneAndCountryCode" />
    <InputClaimsTransformation ReferenceId="ConvertStringToPhoneNumber" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userPrincipalName" />
    <InputClaim ClaimTypeReferenceId="fullPhoneNumber" PartnerClaimType="phoneNumber" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>Zweryfikuj kod

Drugi tryb tego profilu technicznego polega na sprawdzeniu kodu. Dla tego trybu można skonfigurować następujące opcje.

### <a name="input-claims"></a>Oświadczenia wejściowe

Element **InputClaims** zawiera listę oświadczeń do wysłania do usługi Azure AD MFA. Możesz również zmapować nazwę swojego zgłoszenia na nazwę zdefiniowaną w profilu technicznym usługi MFA.

| ClaimReferenceId | Wymagane | Opis |
| --------- | -------- | ----------- | ----------- |
| phoneNumber| Tak | Ten sam numer telefonu, który został wcześniej użyty do wysłania kodu. Służy również do lokalizowania sesji weryfikacyjnej telefonu. |
| verificationCode  | Tak | Kod weryfikacyjny dostarczony przez użytkownika do zweryfikowania |

Element **InputClaimsTransformations** może zawierać kolekcję elementów **InputClaimsTransformation** , które są używane do modyfikowania oświadczeń wejściowych lub generować nowe przed WYWOŁANIEM usługi Azure AD MFA.

### <a name="output-claims"></a>Oświadczenia wyjściowe

Dostawca protokołu usługi Azure AD MFA nie zwraca żadnych **OutputClaims**, więc nie ma potrzeby określania oświadczeń wyjściowych. Można jednak uwzględnić oświadczenia, które nie są zwracane przez dostawcę tożsamości usługi Azure AD MFA, tak długo, jak ustawisz `DefaultValue` atrybut.

Element **OutputClaimsTransformations** może zawierać kolekcję elementów **OutputClaimsTransformation** , które są używane do modyfikowania oświadczeń wyjściowych lub generowania nowych.

### <a name="metadata"></a>Metadane

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Operacja | Tak | Musi być **zweryfikowana** |

#### <a name="ui-elements"></a>Elementy interfejsu użytkownika

Poniższe metadane mogą służyć do konfigurowania komunikatów o błędach wyświetlanych podczas sprawdzania kodu. Metadane należy skonfigurować w profilu technicznym z [własnym potwierdzeniem](self-asserted-technical-profile.md) . Komunikaty o błędach można [lokalizować](localization-string-ids.md#azure-ad-mfa-error-messages).

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| UserMessageIfMaxAllowedCodeRetryReached| Nie | Komunikat o błędzie użytkownika, jeśli użytkownik próbuje wykonać kod weryfikacyjny zbyt wiele razy. |
| UserMessageIfServerError | Nie | Komunikat o błędzie użytkownika, jeśli serwer napotkał błąd wewnętrzny. |
| UserMessageIfThrottled| Nie | Komunikat o błędzie użytkownika, jeśli żądanie jest ograniczone.|
| UserMessageIfWrongCodeEntered| Nie| Komunikat o błędzie użytkownika, jeśli kod wprowadzony do weryfikacji jest nieprawidłowy.|

### <a name="example-verify-a-code"></a>Przykład: Weryfikowanie kodu

Poniższy przykład przedstawia profil techniczny usługi Azure AD MFA używany do sprawdzania kodu.

```xml
<TechnicalProfile Id="AzureMfa-VerifySms">
    <DisplayName>Verify Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="Operation">Verify</Item>
    </Metadata>
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="phoneNumber" PartnerClaimType="phoneNumber" />
        <InputClaim ClaimTypeReferenceId="verificationCode" />
    </InputClaims>
</TechnicalProfile>
```
