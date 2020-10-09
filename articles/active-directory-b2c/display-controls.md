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
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 131ecd010cba55f08199f713654792c0844a47e1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85202300"
---
# <a name="display-controls"></a>Kontrolki wyświetlania

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Kontrolka wyświetlania** to element interfejsu użytkownika, który ma specjalne funkcje i współdziała z usługą zaplecza Azure Active Directory B2C (Azure AD B2C). Umożliwia użytkownikowi wykonywanie akcji na stronie, które wywołują [profil techniczny weryfikacji](validation-technical-profile.md) na zapleczu. Kontrolki wyświetlania są wyświetlane na stronie i są przywoływane przez [własny profil techniczny](self-asserted-technical-profile.md).

Na poniższej ilustracji przedstawiono samodzielną stronę rejestracji z dwoma kontrolkami wyświetlania, które weryfikują podstawowy i pomocniczy adres e-mail.

![Przykład renderowanej kontrolki wyświetlania](media/display-controls/display-control-email.png)

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

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
| InputClaims | 0:1 | **InputClaims** są używane do wstępnego wypełniania wartości oświadczeń, które mają być zbierane od użytkownika. |
| DisplayClaims | 0:1 | **DisplayClaims** są używane do reprezentowania oświadczeń, które mają być zbierane od użytkownika. |
| OutputClaims | 0:1 | **OutputClaims** są używane do reprezentowania oświadczeń do tymczasowego zapisania dla tego elementu **DisplayControl**. |
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

Akcja definiuje listę **profilów technicznych weryfikacji**. Są one używane do sprawdzania poprawności niektórych lub wszystkich oświadczeń wyświetlania kontrolki wyświetlania. Profil techniczny weryfikacji sprawdza poprawność danych wejściowych użytkownika i może zwrócić błąd użytkownika. Możesz użyć **ContinueOnError**, **ContinueOnSuccess**i **warunków** wstępnych w akcji kontrolki Display podobnie jak w przypadku, gdy są one używane w profilach technicznych w ramach [weryfikacji](validation-technical-profile.md) w niepotwierdzonym profilu technicznym.

W poniższym przykładzie kod jest wysyłany w wiadomości e-mail lub wiadomości SMS na podstawie wybranego przez użytkownika żądania **mfaType** .

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
