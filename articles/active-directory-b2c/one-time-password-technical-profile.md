---
title: Włącz weryfikację hasła jednorazowego (OTP)
titleSuffix: Azure AD B2C
description: Dowiedz się, jak skonfigurować scenariusz jednorazowego hasła (OTP) za pomocą zasad niestandardowych Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/19/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6978afc802bddd536c56fcb4e06a40ccc58867fe
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92172667"
---
# <a name="define-a-one-time-password-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Zdefiniuj profil techniczny hasła jednorazowego w zasadach niestandardowych Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) zapewnia obsługę zarządzania generowaniem i weryfikacją hasła jednorazowego. Użyj profilu technicznego, aby wygenerować kod, a następnie sprawdź, czy kod jest późniejszy.

Profil techniczny hasła jednorazowego może również zwrócić komunikat o błędzie podczas weryfikacji kodu. Zaprojektuj integrację z hasłem jednorazowym przy użyciu **profilu technicznego weryfikacji**. Profil techniczny weryfikacji jest wywoływany przy użyciu profilu technicznego hasła jednorazowego w celu zweryfikowania kodu. Profil techniczny weryfikacji weryfikuje dane dostarczone przez użytkownika przed kontynuowaniem podróży użytkownika. W profilu technicznym weryfikacji na stronie z potwierdzeniem zostanie wyświetlony komunikat o błędzie.

## <a name="protocol"></a>Protokół

Atrybut **name** elementu **Protocol** musi być ustawiony na `Proprietary` . Atrybut **programu obsługi** musi zawierać w pełni kwalifikowaną nazwę zestawu programu obsługi protokołu, który jest używany przez Azure AD B2C:

```xml
Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

Poniższy przykład przedstawia profil techniczny hasła jednorazowego:

```xml
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Validate user input verification code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="generate-code"></a>Generowanie kodu

Pierwszy tryb tego profilu technicznego to generowanie kodu. Poniżej przedstawiono opcje, które można skonfigurować dla tego trybu.

### <a name="input-claims"></a>Oświadczenia wejściowe

Element **InputClaims** zawiera listę oświadczeń wymaganych do wysłania do dostawcy protokołu hasła jednorazowego. Możesz również zmapować nazwę swojego zgłoszenia na nazwę zdefiniowaną poniżej.

| ClaimReferenceId | Wymagane | Opis |
| --------- | -------- | ----------- |
| identyfikator | Yes | Identyfikator identyfikujący użytkownika, który musi weryfikować kod później. Jest on często używany jako identyfikator miejsca docelowego, do którego został dostarczony kod, na przykład adres e-mail lub numer telefonu. |

Element **InputClaimsTransformations** może zawierać kolekcję elementów **InputClaimsTransformation** , które są używane do modyfikowania oświadczeń wejściowych lub generować nowe przed wysłaniem do dostawcy protokołu hasła jednorazowego.

### <a name="output-claims"></a>Oświadczenia wyjściowe

Element **OutputClaims** zawiera listę oświadczeń wygenerowanych przez dostawcę protokołu hasła jednorazowego. Możesz również zmapować nazwę swojego zgłoszenia na nazwę zdefiniowaną poniżej.

| ClaimReferenceId | Wymagane | Opis |
| --------- | -------- | ----------- |
| otpGenerated | Yes | Wygenerowany kod, którego sesja jest zarządzana przez Azure AD B2C. |

Element **OutputClaimsTransformations** może zawierać kolekcję elementów **OutputClaimsTransformation** , które są używane do modyfikowania oświadczeń wyjściowych lub generowania nowych.

### <a name="metadata"></a>Metadane

Następujące ustawienia mogą służyć do konfigurowania trybu generowania kodu:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| CodeExpirationInSeconds | Nie | Czas (w sekundach) do wygaśnięcia kodu. Minimum: `60` ; Wartość maksymalna: `1200` ; Wartość domyślna: `600` . Za każdym razem, gdy podano kod (ten sam kod przy użyciu `ReuseSameCode` , lub nowy kod), wygaśnięcie kodu zostanie rozszerzone.  |
| CodeLength | Nie | Długość kodu. Wartość domyślna to `6`. |
| CharacterSet | Nie | Zestaw znaków dla kodu sformatowany do użycia w wyrażeniu regularnym. Na przykład `a-z0-9A-Z`. Wartość domyślna to `0-9`. Zestaw znaków musi zawierać co najmniej 10 różnych znaków w określonym zestawie. |
| NumRetryAttempts | Nie | Liczba prób weryfikacji przed kodem jest uznawana za nieprawidłową. Wartość domyślna to `5`. |
| NumCodeGenerationAttempts | Nie | Liczba maksymalnych prób generowania kodu dla identyfikatora. Wartość domyślna to 10, jeśli nie zostanie określona. |
| Operacja | Yes | Operacja do wykonania. Możliwa wartość: `GenerateCode` . |
| ReuseSameCode | Nie | Czy należy określić ten sam kod zamiast generować nowy kod, gdy dany kod nie wygasł i jest nadal ważny. Wartość domyślna to `false`.  |



### <a name="example"></a>Przykład

Poniższy przykład `TechnicalProfile` służy do generowania kodu:

```xml
<TechnicalProfile Id="GenerateCode">
  <DisplayName>Generate Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">GenerateCode</Item>
    <Item Key="CodeExpirationInSeconds">600</Item>
    <Item Key="CodeLength">6</Item>
    <Item Key="CharacterSet">0-9</Item>
    <Item Key="NumRetryAttempts">5</Item>
    <Item Key="NumCodeGenerationAttempts">15</Item>
    <Item Key="ReuseSameCode">false</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpGenerated" />
  </OutputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>Zweryfikuj kod

Drugi tryb tego profilu technicznego polega na sprawdzeniu kodu. Poniżej przedstawiono opcje, które można skonfigurować dla tego trybu.

### <a name="input-claims"></a>Oświadczenia wejściowe

Element **InputClaims** zawiera listę oświadczeń wymaganych do wysłania do dostawcy protokołu hasła jednorazowego. Możesz również zmapować nazwę swojego zgłoszenia na nazwę zdefiniowaną poniżej.

| ClaimReferenceId | Wymagane | Opis |
| --------- | -------- | ----------- |
| identyfikator | Yes | Identyfikator identyfikujący użytkownika, który wcześniej wygenerował kod. Jest on często używany jako identyfikator miejsca docelowego, do którego został dostarczony kod, na przykład adres e-mail lub numer telefonu. |
| otpToVerify | Yes | Kod weryfikacyjny dostarczony przez użytkownika. |

Element **InputClaimsTransformations** może zawierać kolekcję elementów **InputClaimsTransformation** , które są używane do modyfikowania oświadczeń wejściowych lub generować nowe przed wysłaniem do dostawcy protokołu hasła jednorazowego.

### <a name="output-claims"></a>Oświadczenia wyjściowe

Brak oświadczeń wyjściowych dostarczonych podczas weryfikacji kodu tego dostawcy protokołu.

Element **OutputClaimsTransformations** może zawierać kolekcję elementów **OutputClaimsTransformation** , które są używane do modyfikowania oświadczeń wyjściowych lub generowania nowych.

### <a name="metadata"></a>Metadane

Do trybu weryfikacji kodu można użyć następujących ustawień:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Operacja | Yes | Operacja do wykonania. Możliwa wartość: `VerifyCode` . |


### <a name="ui-elements"></a>Elementy interfejsu użytkownika

Poniższe metadane mogą służyć do konfigurowania komunikatów o błędach wyświetlanych podczas sprawdzania kodu. Metadane należy skonfigurować w profilu technicznym z [własnym potwierdzeniem](self-asserted-technical-profile.md) . Komunikaty o błędach można [lokalizować](localization-string-ids.md#one-time-password-error-messages).

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| UserMessageIfSessionDoesNotExist | Nie | Komunikat, który ma być wyświetlany użytkownikowi, jeśli sesja weryfikacji kodu wygasła. To albo kod wygasł albo kod nigdy nie został wygenerowany dla danego identyfikatora. |
| UserMessageIfMaxRetryAttempted | Nie | Komunikat, który ma być wyświetlany użytkownikowi w przypadku przekroczenia maksymalnej dozwolonej liczby prób weryfikacji. |
| UserMessageIfMaxNumberOfCodeGenerated | Nie | Komunikat, który ma być wyświetlany użytkownikowi w przypadku, gdy generacja kodu przekroczyła maksymalną dozwoloną liczbę prób. |
| UserMessageIfInvalidCode | Nie | Komunikat, który ma być wyświetlany użytkownikowi, jeśli podał nieprawidłowy kod. |
| UserMessageIfVerificationFailedRetryAllowed | Nie | Komunikat, który ma być wyświetlany użytkownikowi w przypadku podania nieprawidłowego kodu, a użytkownik może podać poprawny kod.  |
|UserMessageIfSessionConflict|Nie| Komunikat, który ma być wyświetlany użytkownikowi, jeśli nie można zweryfikować kodu.|

### <a name="example"></a>Przykład

Następujący przykład `TechnicalProfile` służy do weryfikowania kodu:

```xml
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Verify Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">VerifyCode</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
    <InputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpToVerify" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z poniższym artykułem, aby uzyskać przykład użycia profilu technicznego hasła jednorazowego z niestandardową weryfikacją wiadomości e-mail:

- Niestandardowa Weryfikacja poczty e-mail w Azure Active Directory B2C ([Mailjet](custom-email-mailjet.md), [SendGrid](custom-email-sendgrid.md))

