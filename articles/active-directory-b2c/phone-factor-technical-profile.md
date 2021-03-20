---
title: Definiowanie profilu technicznego w ramach niestandardowych zasad
titleSuffix: Azure AD B2C
description: Zdefiniuj profil techniczny w zasadach niestandardowych w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 322e4b78fbfb38f1822fb7a7cdcdbfcc0738b303
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91950401"
---
# <a name="define-a-phone-factor-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiowanie profilu technicznego w Azure Active Directory B2C zasad niestandardowych

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) zapewnia obsługę rejestrowania i weryfikowania numerów telefonów. Ten profil techniczny:

- Udostępnia interfejs użytkownika, który umożliwia współpracę z użytkownikiem w celu sprawdzenia lub zarejestrowania numeru telefonu.
- Obsługuje rozmowy telefoniczne i wiadomości SMS w celu zweryfikowania numeru telefonu.
- Obsługuje wiele numerów telefonów. Użytkownik może wybrać jeden z numerów telefonu do zweryfikowania.  
- Zwraca wartość określającą, czy użytkownik podał nowy numer telefonu. Możesz użyć tego żądania, aby określić, czy numer telefonu ma zostać utrwalony w Azure AD B2C profilu użytkownika.  
- Używa [definicji zawartości](contentdefinitions.md) w celu kontrolowania wyglądu i działania.

## <a name="protocol"></a>Protokół

Atrybut **name** elementu **Protocol** musi być ustawiony na `Proprietary` . Atrybut **programu obsługi** musi zawierać w pełni kwalifikowaną nazwę zestawu programu obsługi protokołu, który jest używany przez Azure AD B2C dla współczynnika telefonu: `Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

Poniższy przykład przedstawia profil techniczny procesu rejestracji i weryfikacji:

```xml
<TechnicalProfile Id="PhoneFactor-InputOrVerify">
  <DisplayName>PhoneFactor</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims-transformations"></a>Przekształcenia oświadczeń wejściowych

Element InputClaimsTransformations może zawierać kolekcję przekształceń oświadczeń wejściowych, które są używane do modyfikowania oświadczeń wejściowych, lub generować nowe. Następująca transformacja oświadczeń wejściowych generuje `UserId` oświadczenie, które jest używane później w kolekcji oświadczeń wejściowych.

```xml
<InputClaimsTransformations>
  <InputClaimsTransformation ReferenceId="CreateUserIdForMFA" />
</InputClaimsTransformations>
```

## <a name="input-claims"></a>Oświadczenia wejściowe

Element InputClaims musi zawierać następujące oświadczenia. Możesz również zmapować nazwę swojego zgłoszenia na nazwę zdefiniowaną w profilu technicznym w ramach technicznego telefonu. 

|  Typ danych| Wymagane | Opis |
| --------- | -------- | ----------- | 
| ciąg| Tak | Unikatowy identyfikator użytkownika. Nazwa lub PartnerClaimType musi być ustawiona na `UserId` . To zgłoszenie nie powinno zawierać informacji osobistych.|
| ciąg| Tak | Lista typów zgłoszeń. Każde z tych roszczeń zawiera jeden numer telefonu. Jeśli którekolwiek z oświadczeń wejściowych nie zawierają numeru telefonu, użytkownik zostanie poproszony o zarejestrowanie i zweryfikowanie nowego numeru telefonu. Sprawdzony numer telefonu jest zwracany jako zgłoszenie wyjściowe. Jeśli jedno z oświadczeń wejściowych zawiera numer telefonu, użytkownik zostanie poproszony o jego zweryfikowanie. Jeśli wiele oświadczeń wejściowych zawiera numer telefonu, użytkownik zostanie poproszony o wybranie i zweryfikowanie jednego z numerów telefonów. |

Poniższy przykład ilustruje użycie wielu numerów telefonów. Aby uzyskać więcej informacji, zobacz temat [przykładowe zasady](https://github.com/azure-ad-b2c/samples/tree/master/policies/mfa-add-secondarymfa).

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
  <InputClaim ClaimTypeReferenceId="secondaryStrongAuthenticationPhoneNumber" />
</InputClaims>
```

## <a name="output-claims"></a>Oświadczenia wyjściowe

Element OutputClaims zawiera listę oświadczeń zwracanych przez profil techniczny czynnika telefonu.

|  Typ danych| Wymagane | Opis |
|  -------- | ----------- |----------- |
| boolean | Tak | Wskazuje, czy nowy numer telefonu został wprowadzony przez użytkownika. Nazwa lub PartnerClaimType musi być ustawiona na `newPhoneNumberEntered`|
| ciąg| Tak | Zweryfikowany numer telefonu. Nazwa lub PartnerClaimType musi być ustawiona na `Verified.OfficePhone` .|

Element OutputClaimsTransformations może zawierać kolekcję elementów OutputClaimsTransformation, które są używane do modyfikowania oświadczeń wyjściowych, lub generować nowe.

## <a name="cryptographic-keys"></a>Klucze kryptograficzne

Element **CryptographicKeys** nie jest używany.


## <a name="metadata"></a>Metadane

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| ContentDefinitionReferenceId | Tak | Identyfikator [definicji zawartości](contentdefinitions.md) skojarzonej z tym profilem technicznym. |
| ManualPhoneNumberEntryAllowed| Nie | Określ, czy użytkownik może ręcznie wprowadzić numer telefonu. Możliwe wartości: `true` , lub `false` (wartość domyślna).|
| Ustawianie. AuthenticationMode | Nie | Metoda weryfikacji numeru telefonu. Możliwe wartości: `sms` , `phone` , lub `mixed` (wartość domyślna).|
| ustawienie. autowybieranie| Nie| Określ, czy profil techniczny ma wybierać autowybieranie, czy wysyłać wiadomości SMS. Możliwe wartości: `true` , lub `false` (wartość domyślna). Funkcja autowybierania wymaga `setting.authenticationMode` , aby metadane były ustawione na `sms` lub `phone` . Kolekcja oświadczeń wejściowych musi mieć pojedynczy numer telefonu. |

### <a name="ui-elements"></a>Elementy interfejsu użytkownika

Elementy interfejsu użytkownika na stronie uwierzytelnianie w ramach wskaźnika telefonu mogą być [lokalizowane](localization-string-ids.md#phone-factor-authentication-page-user-interface-elements).

## <a name="next-steps"></a>Następne kroki

- Sprawdź [konta społecznościowe i lokalne za pomocą pakietu MFA](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa) Starter Pack.
