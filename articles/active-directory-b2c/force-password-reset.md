---
title: Konfigurowanie przepływu resetowania hasła wymuszonego w Azure AD B2C
titleSuffix: Azure AD B2C
description: Dowiedz się, jak skonfigurować przepływ wymuszonego resetowania hasła w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/03/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 0477153b466702bec0fa2d5221fee1e054d06314
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102033784"
---
# <a name="set-up-a-force-password-reset-flow-in-azure-active-directory-b2c"></a>Konfigurowanie przepływu wymuszania resetowania hasła w Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Jako administrator możesz [zresetować hasło użytkownika](manage-users-portal.md#reset-a-users-password) , jeśli użytkownik zapomni hasła. Lub chcesz wymusić Resetowanie hasła. W tym artykule dowiesz się, jak wymusić Resetowanie hasła w tych scenariuszach.

## <a name="overview"></a>Omówienie

Gdy administrator resetuje hasło użytkownika za pośrednictwem Azure Portal, wartość atrybutu [forceChangePasswordNextSignIn](user-profile-attributes.md#password-profile-property) jest ustawiona na `true` .

Ta wartość atrybutu jest sprawdzana [podczas logowania i wylogowania](add-sign-up-and-sign-in-policy.md) . Jeśli użytkownik ukończy logowanie, jeśli atrybut jest ustawiony na `true` , użytkownik musi zresetować swoje hasło. Następnie wartość atrybutu jest ustawiona na z powrotem `false` .

![Wymuś przepływ resetowania hasła](./media/force-password-reset/force-password-reset-flow.png)

Przepływ resetowania hasła dotyczy kont lokalnych w Azure AD B2C, które używają [adresu e-mail](identity-provider-local.md#email-sign-in) lub [nazwy użytkownika](identity-provider-local.md#username-sign-in) z hasłem do logowania.

### <a name="force-a-password-reset-after-90-days"></a>Wymuś Resetowanie hasła po 90 dniach

Jako administrator możesz ustawić czas wygaśnięcia hasła użytkownika na 90 dni przy użyciu programu [MS Graph](microsoft-graph-operations.md). Po 90 dniach wartość atrybutu [forceChangePasswordNextSignIn](user-profile-attributes.md#password-profile-property) jest automatycznie ustawiana na `true` . Aby uzyskać więcej informacji na temat sposobu ustawiania zasad wygasania haseł użytkownika, zobacz [atrybut zasad haseł](user-profile-attributes.md#password-policy-attribute).

Po ustawieniu zasad wygasania haseł należy również skonfigurować wymuszanie przepływu resetowania hasła, zgodnie z opisem w tym artykule.  

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="configure-your-policy"></a>Konfigurowanie zasad

::: zone pivot="b2c-user-flow"

Aby włączyć ustawienie **wymuszone Resetowanie hasła** w przepływie użytkownika dotyczącego tworzenia konta lub logowania:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Na pasku narzędzi portalu wybierz ikonę **katalog i subskrypcję** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. Wybierz pozycję **przepływy użytkownika**.
1. Wybierz pozycję Rejestrowanie i logowanie lub przepływ użytkowników logowania ( **zalecane** typy), które chcesz dostosować.
1. W menu po lewej stronie w obszarze **Ustawienia** wybierz pozycję **Właściwości**.
1. W obszarze **złożoność hasła** wybierz pozycję **wymuszone Resetowanie hasła**.
1. Wybierz pozycję **Zapisz**.

### <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako administrator użytkownika lub administrator haseł. Aby uzyskać więcej informacji na temat dostępnych ról, zobacz [Przypisywanie ról administratorów w Azure Active Directory](../active-directory/roles/permissions-reference.md#all-roles).
1. Na pasku narzędzi portalu wybierz ikonę **katalog i subskrypcję** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. Wybierz pozycję **Użytkownicy**. Wyszukaj i wybierz użytkownika, którego będziesz używać do testowania resetowania hasła, a następnie wybierz pozycję **Resetuj hasło**.
1. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. Wybierz pozycję **przepływy użytkownika**.
1. Wybierz przepływ użytkowników do rejestracji lub logowania ( **zalecane** typy), które chcesz przetestować.
1. Wybierz pozycję **Uruchom przepływ użytkownika**.
1. W przypadku **aplikacji** wybierz aplikację sieci Web o nazwie *webapp1* , która została wcześniej zarejestrowana. Powinien być pokazywany **adres URL odpowiedzi** `https://jwt.ms` .
1. Wybierz pozycję **Uruchom przepływ użytkownika**.
1. Zaloguj się przy użyciu konta użytkownika, dla którego chcesz zresetować hasło.
1. Teraz należy zmienić hasło użytkownika. Zmień hasło i wybierz pozycję **Kontynuuj**. Token jest zwracany do `https://jwt.ms` i powinien być wyświetlony.

::: zone-end

::: zone pivot="b2c-custom-policy"

1. Zapoznaj się z przykładem wymuszenia resetowania hasła w serwisie [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/policies/force-password-reset).
1. W każdym pliku Zastąp ciąg `yourtenant` nazwą dzierżawy Azure AD B2C. Na przykład jeśli nazwa dzierżawy usługi B2C jest *contosob2c*, wszystkie wystąpienia `yourtenant.onmicrosoft.com` stają się dostępne `contosob2c.onmicrosoft.com` .
1. Przekaż pliki zasad w następującej kolejności: zasady rozszerzenia `TrustFrameworkExtensionsCustomForcePasswordReset.xml` , a następnie zasady jednostki uzależnionej `SignUpOrSigninCustomForcePasswordReset.xml` .

### <a name="test-the-policy"></a>Testowanie zasad

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako administrator użytkownika lub administrator haseł. Aby uzyskać więcej informacji na temat dostępnych ról, zobacz [Przypisywanie ról administratorów w Azure Active Directory](../active-directory/roles/permissions-reference.md#all-roles).
1. Na pasku narzędzi portalu wybierz ikonę **katalog i subskrypcję** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. Wybierz pozycję **Użytkownicy**. Wyszukaj i wybierz użytkownika, którego będziesz używać do testowania resetowania hasła, a następnie wybierz pozycję **Resetuj hasło**.
1. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. W obszarze **zasady** wybierz pozycję **platforma obsługi tożsamości**.
1. Wybierz `B2C_1A_signup_signin_Custom_ForcePasswordReset` zasady, aby je otworzyć. 
1. W przypadku **aplikacji** wybierz [wcześniej zarejestrowaną](troubleshoot-custom-policies.md#troubleshoot-the-runtime)aplikację sieci Web. Powinien być pokazywany **adres URL odpowiedzi** `https://jwt.ms` .
1. Wybierz przycisk **Uruchom teraz** .
1. Zaloguj się przy użyciu konta użytkownika, dla którego chcesz zresetować hasło.
1. Teraz należy zmienić hasło użytkownika. Zmień hasło i wybierz pozycję **Kontynuuj**. Token jest zwracany do `https://jwt.ms` i powinien być wyświetlony.

::: zone-end

## <a name="next-steps"></a>Następne kroki

Skonfiguruj samoobsługowe [Resetowanie hasła](add-password-reset-policy.md).
