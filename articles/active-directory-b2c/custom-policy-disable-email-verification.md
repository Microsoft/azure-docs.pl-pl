---
title: Wyłącz weryfikację poczty e-mail podczas rejestracji klienta przy użyciu zasad niestandardowych
titleSuffix: Azure AD B2C
description: Dowiedz się, jak wyłączyć weryfikację poczty e-mail podczas tworzenia konta klienta w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 13a5fa6a030d876d92651ca587e37fdc6a3ec600
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79136146"
---
# <a name="disable-email-verification-during-customer-sign-up-using-a-custom-policy-in-azure-active-directory-b2c"></a>Wyłącz weryfikację poczty e-mail podczas rejestracji klienta przy użyciu zasad niestandardowych w programie Azure Active Directory B2C

[!INCLUDE [disable email verification intro](../../includes/active-directory-b2c-disable-email-verification.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki opisane w temacie Wprowadzenie [do zasad niestandardowych](custom-policy-get-started.md). Należy mieć działającą niestandardową zasadę do rejestracji i logowania przy użyciu kont społecznościowych i lokalnych.

## <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>Dodawanie metadanych do profilu technicznego z własnym potwierdzeniem

Profil techniczny **LocalAccountSignUpWithLogonEmail** jest [automatycznie potwierdzony](self-asserted-technical-profile.md), który jest wywoływany podczas przepływu rejestracji. Aby wyłączyć weryfikację adresu e-mail, ustaw `EnforceEmailVerification` dla metadanych wartość false. Zastąp profile techniczne LocalAccountSignUpWithLogonEmail w pliku rozszerzenia. 

1. Otwórz plik rozszerzeń zasad. Na przykład <em> `SocialAndLocalAccounts/` </em>.
1. Znajdź `ClaimsProviders` element. Jeśli element nie istnieje, Dodaj go.
1. Dodaj następującego dostawcę oświadczeń do `ClaimsProviders` elementu:

```XML
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <Metadata>
        <Item Key="EnforceEmailVerification">false</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="test-the-custom-policy"></a>Testowanie zasad niestandardowych

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD, wybierając filtr **katalog + subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawę usługi Azure AD.
3. Wybierz pozycję **wszystkie usługi** w lewym górnym rogu Azure Portal, a następnie wyszukaj i wybierz pozycję **rejestracje aplikacji**.
4. Wybierz pozycję **platforma obsługi tożsamości**.
5. Wybierz pozycję **Przekaż zasady niestandardowe**, a następnie Przekaż dwa zmienione pliki zasad.
2. Wybierz przekazane zasady rejestracji lub logowania, a następnie kliknij przycisk **Uruchom teraz** .
3. Należy mieć możliwość rejestrowania się przy użyciu adresu e-mail bez sprawdzania poprawności.


## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [niepotwierdzonym profilu technicznym](self-asserted-technical-profile.md) w dokumentacji IEF.
