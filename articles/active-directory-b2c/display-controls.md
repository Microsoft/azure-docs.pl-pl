---
title: Wyświetl informacje o kontrolce
titleSuffix: Azure AD B2C
description: Informacje dotyczące kontrolek wyświetlania Azure AD B2C. Użyj kontrolek wyświetlania do dostosowywania podróży użytkowników zdefiniowanych w niestandardowych zasadach.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 441a77823c77305e567e9e1436715bc51ca48c11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97387058"
---
# <a name="display-controls"></a>Kontrolki wyświetlania

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Kontrolka wyświetlania** to element interfejsu użytkownika, który ma specjalne funkcje i współdziała z usługą zaplecza Azure Active Directory B2C (Azure AD B2C). Umożliwia użytkownikowi wykonywanie akcji na stronie, które wywołują [profil techniczny weryfikacji](validation-technical-profile.md) na zapleczu. Kontrolki wyświetlania są wyświetlane na stronie i są przywoływane przez [własny profil techniczny](self-asserted-technical-profile.md).

Na poniższej ilustracji przedstawiono samodzielną stronę rejestracji z dwoma kontrolkami wyświetlania, które weryfikują podstawowy i pomocniczy adres e-mail.

![Przykład renderowanej kontrolki wyświetlania](media/display-controls/display-control-email.png)

## <a name="prerequisites"></a>Wymagania wstępne

 W sekcji [Metadata](self-asserted-technical-profile.md#metadata) w [profilu technicznym](self-asserted-technical-profile.md), do którego istnieje odwołanie, [ContentDefinition](contentdefinitions.md) musi mieć `DataUri` ustawiony na stronę z wersją 2.0.0 lub wyższą. Na przykład:

```xml
<ContentDefinition Id="api.selfasserted">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:2.0.0</DataUri>
  ...
```

## <a name="defining-display-controls"></a>Definiowanie formantów wyświetlania

Element **DisplayControl** zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Id | Tak | Identyfikator używany do kontrolki wyświetlania. Można się [do niego odwoływać](#referencing-display-controls). |
| UserInterfaceControlType | Tak | Typ kontrolki wyświetlania. Obecnie obsługiwane jest [VerificationControl](display-control-verification.md) |

Element **DisplayControl** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| InputClaims | 0:1 | **InputClaims** są używane do wstępnego wypełniania wartości oświadczeń, które mają być zbierane od użytkownika. Aby uzyskać więcej informacji, zobacz [InputClaims](technicalprofiles.md#input-claims) element. |
| DisplayClaims | 0:1 | **DisplayClaims** są używane do reprezentowania oświadczeń, które mają być zbierane od użytkownika. Aby uzyskać więcej informacji, zobacz [DisplayClaim](technicalprofiles.md#displayclaim) element.|
| OutputClaims | 0:1 | **OutputClaims** są używane do reprezentowania oświadczeń do tymczasowego zapisania dla tego elementu **DisplayControl**. Aby uzyskać więcej informacji, zobacz [OutputClaims](technicalprofiles.md#output-claims) element.|
| Akcje | 0:1 | **Akcje** są używane do wyświetlania listy profilów technicznych walidacji, które mają być wywoływane w przypadku akcji użytkownika na frontonie. |

### <a name="input-claims"></a>Oświadczenia wejściowe

W kontrolce wyświetlania można użyć elementów **InputClaims** , aby wstępnie wypełnić wartość oświadczeń do zebrania od użytkownika na stronie. Dowolne **InputClaimsTransformations** można zdefiniować w profilu technicznym, który odwołuje się do tej kontrolki wyświetlania.

Poniższy przykład wstępnie wypełnia adres e-mail, który ma być zweryfikowany za pomocą już istniejącego adresu.

```xml
<DisplayControl Id="emailControl" UserInterfaceControlType="VerificationControl">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="emailAddress" />
  </InputClaims>
  ...
```

### <a name="display-claims"></a>Wyświetl oświadczenia

Każdy typ kontrolki wyświetlania wymaga innego zestawu oświadczeń wyświetlania, [oświadczeń wyjściowych](#output-claims)i [akcji](#display-control-actions) , które mają zostać wykonane.

Podobnie jak w przypadku **oświadczeń wyświetlanych** zdefiniowanych w [profilu technicznym z własnym potwierdzeniem](self-asserted-technical-profile.md#display-claims), oświadczenia wyświetlane reprezentują oświadczenia, które mają być zbierane od użytkownika w kontrolce wyświetlania. Element **ClaimType** , do którego istnieje odwołanie, musi określać element **UserInputType** dla typu danych wejściowych użytkownika obsługiwanego przez Azure AD B2C, na przykład `TextBox` lub `DropdownSingleSelect` . Jeśli wartość żądania wyświetlania jest wymagana przez **akcję**, należy ustawić **wymagany** atrybut, aby `true` wymusić użytkownikowi podanie wartości dla tego konkretnego żądania wyświetlania.

Niektóre typy kontrolek wyświetlania są wymagane przez określone oświadczenia ekranu. Na przykład **VerificationCode** jest wymagany dla kontrolki Display typu **VerificationControl**. Użyj atrybutu **ControlClaimType** , aby określić, który DisplayClaim jest wyznaczono dla tego wymaganego żądania. Na przykład:

```xml
<DisplayClaim ClaimTypeReferenceId="otpCode" ControlClaimType="VerificationCode" Required="true" />
```

### <a name="output-claims"></a>Oświadczenia wyjściowe

**Oświadczenia wyjściowe** kontrolki wyświetlania nie są wysyłane do następnego kroku aranżacji. Są one zapisywane tymczasowo tylko dla bieżącej sesji kontrolki wyświetlania. Te tymczasowe oświadczenia mogą być współużytkowane między różnymi akcjami tej samej kontrolki wyświetlania.

Aby wyrównać dane wyjściowe oświadczeń do kolejnego kroku aranżacji, należy użyć **OutputClaims** rzeczywistego profilu technicznego z własnym potwierdzeniem, który odwołuje się do tej kontrolki wyświetlania.

### <a name="display-control-actions"></a>Akcje kontrolki wyświetlania

**Akcje** kontrolki wyświetlania są procedurami, które wystąpiły w Azure AD B2C zaplecza, gdy użytkownik wykonuje określoną akcję po stronie klienta (w przeglądarce). Na przykład walidacji są wykonywane, gdy użytkownik wybierze przycisk na stronie.

Akcja definiuje listę **profilów technicznych weryfikacji**. Są one używane do sprawdzania poprawności niektórych lub wszystkich oświadczeń wyświetlania kontrolki wyświetlania. Profil techniczny weryfikacji sprawdza poprawność danych wejściowych użytkownika i może zwrócić błąd użytkownika. Możesz użyć **ContinueOnError**, **ContinueOnSuccess** i **warunków** wstępnych w akcji kontrolki Display podobnie jak w przypadku, gdy są one używane w profilach technicznych w ramach [weryfikacji](validation-technical-profile.md) w niepotwierdzonym profilu technicznym.

#### <a name="actions"></a>Akcje

Element **Actions** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Akcja | 1: n | Lista akcji do wykonania. |

#### <a name="action"></a>Akcja

Element **Action** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Id | Tak | Typ operacji. Możliwe wartości: `SendCode` lub `VerifyCode` . `SendCode`Wartość wysyła kod do użytkownika. Ta akcja może zawierać dwie profile techniczne weryfikacji: jeden do wygenerowania kodu i jeden do jego wysłania. `VerifyCode`Wartość weryfikuje kod wprowadzony przez użytkownika w wejściowym polu tekstowym. |

Element **Action** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| ValidationClaimsExchange | 1:1 | Identyfikatory profilów technicznych, które są używane do weryfikowania niektórych lub wszystkich oświadczeń wyświetlania w profilu technicznym, do którego się odwołuje. Wszystkie oświadczenia wejściowe w profilu technicznym, do którego się odwołuje, muszą pojawić się w oświadczeniach o podanym profilu technicznym. |

#### <a name="validationclaimsexchange"></a>ValidationClaimsExchange

Element **ValidationClaimsExchange** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1: n | Profil techniczny, który będzie używany do sprawdzania poprawności niektórych lub wszystkich oświadczeń wyświetlania w profilu technicznym, do którego się odwołuje. |

Element **ValidationTechnicalProfile** zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Identyfikator odwołania | Tak | Identyfikator profilu technicznego jest już zdefiniowany w zasadach lub zasadach nadrzędnych. |
|ContinueOnError|Nie| Wskazuje, czy sprawdzanie poprawności wszelkich kolejnych weryfikacji profilów technicznych powinno być kontynuowane, jeśli ten profil techniczny tego sprawdzania poprawności zgłasza błąd. Możliwe wartości: `true` lub `false` (domyślnie przetwarzanie dalszych profilów weryfikacyjnych zostanie zatrzymane i zostanie zwrócony błąd). |
|ContinueOnSuccess | Nie | Wskazuje, czy sprawdzanie poprawności wszelkich kolejnych profilów weryfikacji powinno być kontynuowane, jeśli ten profil techniczny zostanie pomyślnie zakończony. Możliwe wartości: `true` lub `false` . Wartość domyślna to `true` , co oznacza, że przetwarzanie dalszych profilów weryfikacji będzie kontynuowane. |

Element **ValidationTechnicalProfile** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Warunki wstępne | 0:1 | Lista warunków wstępnych, które muszą być spełnione, aby można było wykonać walidację profilu technicznego. |

Element **Conditional** zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| `Type` | Tak | Typ sprawdzenia lub zapytania do wykonania dla warunku wstępnego. Możliwe wartości: `ClaimsExist` lub `ClaimEquals` . `ClaimsExist` Określa, czy akcje mają być wykonywane, jeśli określone oświadczenia istnieją w bieżącym zestawie oświadczeń użytkownika. `ClaimEquals` Określa, że akcje należy wykonać, jeśli istnieje określone zastrzeżenie i jego wartość jest równa określonej wartości. |
| `ExecuteActionsIf` | Tak | Wskazuje, czy akcje w warunku wstępnym mają być wykonywane, jeśli test ma wartość true lub false. |

Element **Conditional** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Wartość | 1: n | Dane, które są używane przez sprawdzanie. Jeśli typ tego sprawdzenia to `ClaimsExist` , to pole określa ClaimTypeReferenceId do zapytania. Jeśli typ sprawdzenia to `ClaimEquals` , to pole określa ClaimTypeReferenceId do zapytania. Określ wartość do sprawdzenia w innym elemencie wartości.|
| Akcja | 1:1 | Akcja, która powinna zostać podjęta, jeśli sprawdzanie warunków wstępnych w ramach kroku aranżacji ma wartość true. Wartość **akcji** jest ustawiona na `SkipThisValidationTechnicalProfile` , która określa, że skojarzony profil techniczny weryfikacji nie powinien być wykonywany. |

Poniższy przykład wysyła i weryfikuje adres e-mail przy użyciu [profilu technicznego usługi Azure AD SSPR](aad-sspr-technical-profile.md).

```xml
<DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
  <InputClaims></InputClaims>
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="email" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
  </DisplayClaims>
  <OutputClaims></OutputClaims>
  <Actions>
    <Action Id="SendCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AadSspr-SendCode" />
      </ValidationClaimsExchange>
    </Action>
    <Action Id="VerifyCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AadSspr-VerifyCode" />
      </ValidationClaimsExchange>
    </Action>
  </Actions>
</DisplayControl>
```

W poniższym przykładzie kod jest wysyłany w wiadomości e-mail lub wiadomości SMS na podstawie wybranych przez użytkownika roszczeń **mfaType** z warunkami wstępnymi.

```xml
<Action Id="SendCode">
  <ValidationClaimsExchange>
    <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AzureMfa-SendSms">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>mfaType</Value>
          <Value>email</Value>
          <Action>SkipThisValidationTechnicalProfile</Action>
        </Precondition>
      </Preconditions>
    </ValidationClaimsExchangeTechnicalProfile>
    <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AadSspr-SendEmail">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>mfaType</Value>
          <Value>phone</Value>
          <Action>SkipThisValidationTechnicalProfile</Action>
        </Precondition>
      </Preconditions>
    </ValidationClaimsExchangeTechnicalProfile>
  </ValidationClaimsExchange>
</Action>
```

## <a name="referencing-display-controls"></a>Odwołania do kontrolek wyświetlania

Kontrolki wyświetlania są przywoływane w [wyświetlanych oświadczeniach](self-asserted-technical-profile.md#display-claims) [profilu technicznego z potwierdzeniem](self-asserted-technical-profile.md).

Na przykład:

```xml
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
  ...
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim DisplayControlReferenceId="PhoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
```

## <a name="next-steps"></a>Następne kroki

Przykłady korzystania z kontrolki Display można znaleźć w temacie: 

- [Niestandardowa Weryfikacja poczty e-mail za pomocą Mailjet](custom-email-mailjet.md)
- [Niestandardowa Weryfikacja poczty e-mail za pomocą SendGrid](custom-email-sendgrid.md)
