---
title: Wyłącz weryfikację wiadomości e-mail podczas rejestracji klienta
titleSuffix: Azure AD B2C
description: Dowiedz się, jak wyłączyć weryfikację poczty e-mail podczas tworzenia konta klienta w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 0f70c8d501a7d56f4bc29e0f2b065760cad625e5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97585024"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>Wyłącz weryfikację poczty e-mail podczas rejestracji klienta w Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Domyślnie program Azure Active Directory B2C (Azure AD B2C) weryfikuje adres e-mail klienta dla kont lokalnych (konta użytkowników logujących się przy użyciu adresu e-mail lub nazwy użytkownika). Azure AD B2C zapewnia prawidłowe adresy e-mail, wymagając od klientów zweryfikowania ich podczas procesu rejestracji. Zapobiega to również złośliwym podmiotom korzystającym z zautomatyzowanych procesów do generowania fałszywych kont w aplikacjach.

Niektórzy deweloperzy aplikacji wolą pominąć weryfikację wiadomości e-mail podczas procesu rejestracji, a zamiast tego ponownie sprawdzić swój adres e-mail przez klientów. Aby to umożliwić, Azure AD B2C można skonfigurować w celu wyłączenia weryfikacji wiadomości e-mail. Dzięki temu program tworzy płynny proces tworzenia konta i zapewnia deweloperom elastyczność w odróżnieniu od klientów, którzy sprawdzili swój adres e-mail od klientów.

> [!WARNING]
> Wyłączenie weryfikacji poczty e-mail w procesie tworzenia konta może prowadzić do spamu. W przypadku wyłączenia domyślnej weryfikacji poczty e-mail dostarczonej przez Azure AD B2C zalecamy zaimplementowanie systemu weryfikacji zamiennej.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]
## <a name="disable-email-verification"></a>Wyłączanie weryfikacji e-mail

::: zone pivot="b2c-user-flow"

Wykonaj następujące kroki, aby wyłączyć weryfikację wiadomości e-mail:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
1. Użyj filtru **katalogów i subskrypcji** w górnym menu, aby wybrać katalog, który zawiera dzierżawę Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Lub wybierz pozycję **wszystkie usługi** i Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. Wybierz pozycję **przepływy użytkownika**.
1. Wybierz przepływ użytkownika, dla którego chcesz wyłączyć weryfikację wiadomości e-mail. Na przykład *B2C_1_signinsignup*.
1. Wybierz pozycję **układy strony**.
1. Wybierz **stronę rejestracji konta lokalnego**.
1. W obszarze **atrybuty użytkownika** wybierz opcję **adres e-mail**.
1. Z listy rozwijanej **wymaganie weryfikacji** wybierz pozycję **nie**.
1. Wybierz pozycję **Zapisz**. Weryfikacja poczty e-mail jest teraz wyłączona dla tego przepływu użytkownika.

::: zone-end

::: zone pivot="b2c-custom-policy"
Profil techniczny **LocalAccountSignUpWithLogonEmail** jest [automatycznie potwierdzony](self-asserted-technical-profile.md), który jest wywoływany podczas przepływu rejestracji. Aby wyłączyć weryfikację adresu e-mail, ustaw `EnforceEmailVerification` dla metadanych wartość false. Zastąp profile techniczne LocalAccountSignUpWithLogonEmail w pliku rozszerzenia. 

1. Otwórz plik rozszerzeń zasad. Na przykład <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Znajdź `ClaimsProviders` element. Jeśli element nie istnieje, Dodaj go.
1. Dodaj następującego dostawcę oświadczeń do `ClaimsProviders` elementu:

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <Metadata>
        <Item Key="EnforceEmailVerification">false</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```
::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="test-your-policy"></a>Testowanie zasad 

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
1. Użyj filtru **katalogów i subskrypcji** w górnym menu, aby wybrać katalog, który zawiera dzierżawę Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Lub wybierz pozycję **wszystkie usługi** i Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. Wybierz pozycję **przepływy użytkownika**.
1. Wybierz przepływ użytkownika, dla którego chcesz wyłączyć weryfikację wiadomości e-mail. Na przykład *B2C_1_signinsignup*.
1. Aby przetestować zasady, wybierz pozycję **Uruchom przepływ użytkownika**.
1. W przypadku **aplikacji** wybierz aplikację sieci Web o nazwie *testapp1* , która została wcześniej zarejestrowana. Powinien być pokazywany **adres URL odpowiedzi** `https://jwt.ms` .
1. Kliknij pozycję **Uruchom przepływ użytkownika**
1. Należy mieć możliwość rejestrowania się przy użyciu adresu e-mail bez sprawdzania poprawności.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="update-and-test-the-relying-party-file"></a>Aktualizowanie i testowanie pliku jednostki uzależnionej

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD, wybierając filtr **katalog + subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawę usługi Azure AD.
1. Wybierz pozycję **wszystkie usługi** w lewym górnym rogu Azure Portal, a następnie wyszukaj i wybierz pozycję **rejestracje aplikacji**.
1. Wybierz pozycję **platforma obsługi tożsamości**.
1. Wybierz pozycję **Przekaż zasady niestandardowe**, a następnie Przekaż dwa zmienione pliki zasad.
1. Wybierz przekazane zasady rejestracji lub logowania, a następnie kliknij przycisk **Uruchom teraz** .
1. Należy mieć możliwość rejestrowania się przy użyciu adresu e-mail bez sprawdzania poprawności.

::: zone-end


## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [dostosować interfejs użytkownika w Azure Active Directory B2C](customize-ui-with-html.md)

