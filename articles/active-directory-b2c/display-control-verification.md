---
title: Weryfikuj oświadczenia z kontrolkami wyświetlania
titleSuffix: Azure AD B2C
description: Dowiedz się, jak za pomocą kontrolek wyświetlania Azure AD B2C sprawdzić oświadczenia w podróży użytkowników udostępniane przez zasady niestandardowe.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e42c889277f937a33e72eaf57819385166d6a409
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85202318"
---
# <a name="verification-display-control"></a>Kontrolka wyświetlania weryfikacji

Za pomocą [kontrolki wyświetlania](display-controls.md) weryfikacyjnej Sprawdź, czy na przykład adres e-mail lub numer telefonu zawiera kod weryfikacyjny, który został wysłany do użytkownika.

## <a name="verificationcontrol-actions"></a>Akcje VerificationControl

Kontrolka wyświetlania weryfikacji składa się z dwóch kroków (czynności):

1. Zażądaj miejsca docelowego od użytkownika, takiego jak adres e-mail lub numer telefonu, na który ma zostać wysłany kod weryfikacyjny. Gdy użytkownik wybierze przycisk **Wyślij kod** , jest wykonywana **Akcja kontrolka sendcode** kontrolki wyświetlania weryfikacji. **Akcja kontrolka sendcode** generuje kod, konstruuje zawartość do wysłania i wysyła ją do użytkownika. Wartość adresu może być wstępnie wypełniona i służyć jako uwierzytelnianie dwuskładnikowe.

    ![Przykładowa strona dla akcji wysyłania kodu](media/display-control-verification/display-control-verification-email-action-01.png)

1. Po wysłaniu kodu użytkownik odczytuje komunikat, wprowadza kod weryfikacyjny do kontrolki dostarczonej przez formant wyświetlania i wybiera polecenie **Weryfikuj kod**. Wybierając pozycję **Weryfikuj kod**, **Akcja kontrolka verifycode** jest wykonywana w celu zweryfikowania kodu skojarzonego z adresem. Jeśli użytkownik wybierze pozycję **Wyślij nowy kod**, pierwsza akcja zostanie wykonana ponownie.

    ![Przykładowa strona dla akcji Weryfikuj kod](media/display-control-verification/display-control-verification-email-action-02.png)

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="verificationcontrol-required-elements"></a>VerificationControl elementy wymagane

**VerificationControl** musi zawierać następujące elementy:

- Typ elementu `DisplayControl` to `VerificationControl` .
- `DisplayClaims`
  - **Wyślij do** -co najmniej jedno oświadczenie określające miejsce, do którego ma zostać wysłany kod weryfikacyjny. Na przykład *adres e-mail* lub *kod kraju* i *numer telefonu*.
  - **Kod weryfikacyjny** — Kontrola kodu weryfikacyjnego, którą użytkownik udostępnia po wysłaniu kodu. To żądanie musi być ustawione jako wymagane i `ControlClaimType` musi mieć ustawioną wartość `VerificationCode` .
- Zgłoszenie wyjściowe (opcjonalne) do zwrócenia do strony z własnym poproszonym po zakończeniu procesu weryfikacji przez użytkownika. Na przykład *adres e-mail* lub *kod kraju* i *numer telefonu*. Profil techniczny z własnym potwierdzeniem używa oświadczeń do utrwalania danych lub przechodzenia do następnego kroku aranżacji.
- Dwa `Action` s z następującymi nazwami:
  - **Kontrolka sendcode** — wysyła kod do użytkownika. Ta akcja zwykle zawiera dwa sprawdzanie poprawności profilu technicznego w celu wygenerowania kodu i wysłania go.
  - **Kontrolka verifycode** — weryfikuje kod. Ta akcja zwykle zawiera profil techniczny weryfikacji pojedynczej.

W poniższym przykładzie pole tekstowe **wiadomości e-mail** jest wyświetlane na stronie. Gdy użytkownik wprowadzi swój adres e-mail i wybierze **kontrolka sendcode**, Akcja **kontrolka sendcode** zostanie wyzwolona w Azure AD B2C zaplecza.

Następnie użytkownik wprowadza **verificationCode** i wybiera **kontrolka verifycode** , aby wyzwolić akcję **kontrolka verifycode** w zapleczu. Jeśli wszystkie walidacje zakończyły się powodzeniem, **VerificationControl** jest uznawany za kompletne i użytkownik może przejść do następnego kroku.

```xml
<DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="email"  Required="true" />
    <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
  </OutputClaims>
  <Actions>
    <Action Id="SendCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="GenerateOtp" />
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendGrid" />
      </ValidationClaimsExchange>
    </Action>
    <Action Id="VerifyCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="VerifyOtp" />
      </ValidationClaimsExchange>
    </Action>
  </Actions>
</DisplayControl>
```
