---
title: Konfigurowanie przepływu rejestracji i logowania
titleSuffix: Azure AD B2C
description: Dowiedz się, jak skonfigurować przepływ rejestracji i logowania w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/16/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 047b295d68e872b4dc334e53b5b9da0ec07d5826
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257079"
---
# <a name="set-up-a-sign-up-and-sign-in-flow-in-azure-active-directory-b2c"></a>Konfigurowanie przepływu rejestracji i logowania w Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="sign-up-and-sign-in-flow"></a>Przepływ i logowanie

Zasady rejestracji i logowania umożliwiają użytkownikom: 

* Utwórz konto przy użyciu konta lokalnego
* Zaloguj się przy użyciu konta lokalnego
* Rejestracja lub logowanie przy użyciu konta społecznościowego
* Resetowanie hasła

![Przepływ edycji profilu](./media/add-sign-up-and-sign-in-policy/add-sign-up-and-sign-in-flow.png)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli jeszcze tego nie zrobiono, [zarejestruj aplikację sieci Web w Azure Active Directory B2C](tutorial-register-applications.md).

::: zone pivot="b2c-user-flow"

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Tworzenie przepływu użytkownika dotyczącego rejestracji i logowania

Przepływ użytkowników rejestracji i logowania obsługuje zarówno środowisko tworzenia konta, jak i logowania przy użyciu jednej konfiguracji. Użytkownicy Twojej aplikacji nie wyłączają właściwej ścieżki w zależności od kontekstu.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Na pasku narzędzi portalu wybierz ikonę **katalog i subskrypcję** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. W obszarze **zasady** wybierz pozycję **przepływy użytkowników**, a następnie wybierz pozycję **Nowy przepływ użytkownika**.

    ![Strona przepływy użytkownika w portalu z wyróżnionym przyciskiem nowy przepływ użytkownika](./media/add-sign-up-and-sign-in-policy/signup-signin-user-flow.png)

1. Na stronie **Tworzenie przepływu użytkownika** wybierz pozycję **zarejestruj się i zaloguj się** .

    ![Wybierz stronę przepływu użytkownika z wyróżnioną pozycją Zarejestruj się i zaloguj się](./media/add-sign-up-and-sign-in-policy/select-user-flow-type.png)

1. W obszarze **Wybierz wersję** wybierz pozycję **zalecane**, a następnie wybierz pozycję **Utwórz**. ([Dowiedz się więcej](user-flow-versions.md) o wersjach przepływu użytkowników).

    ![Utwórz stronę przepływu użytkownika w Azure Portal z wyróżnionymi właściwościami](./media/add-sign-up-and-sign-in-policy/select-version.png)

1. Wprowadź **nazwę** przepływu użytkownika. Na przykład *signupsignin1*.
1. W przypadku **dostawców tożsamości** wybierz pozycję **rejestracja w wiadomości e-mail**.
1. W polu **atrybuty użytkownika i oświadczenia** wybierz oświadczenia i atrybuty, które mają być zbierane i wysyłane przez użytkownika podczas tworzenia konta. Na przykład wybierz pozycję **Pokaż więcej**, a następnie wybierz atrybuty i oświadczenia dla **kraju/regionu**, **Nazwa wyświetlana** i **Kod pocztowy**. Kliknij przycisk **OK**.

    ![Strona wyboru atrybutów i oświadczeń z trzema wybranymi oświadczeniami](./media/add-sign-up-and-sign-in-policy/signup-signin-attributes.png)

1. Kliknij przycisk **Utwórz** , aby dodać przepływ użytkownika. Prefiks *B2C_1* jest automatycznie dołączany do nazwy.
2. Postępuj zgodnie z instrukcjami, aby [obsłużyć przepływ "nie pamiętasz hasła?"](add-password-reset-policy.md?pivots=b2c-user-flow.md#self-service-password-reset-recommended) . w zasadach rejestracji lub logowania.

### <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

1. Wybierz utworzony przepływ użytkownika, aby otworzyć jego stronę przeglądu, a następnie wybierz pozycję **Uruchom przepływ użytkownika**.
1. W przypadku **aplikacji** wybierz aplikację sieci Web o nazwie *webapp1* , która została wcześniej zarejestrowana. Powinien być pokazywany **adres URL odpowiedzi** `https://jwt.ms` .
1. Kliknij pozycję **Uruchom przepływ użytkownika**, a następnie wybierz pozycję **Utwórz konto teraz**.

    ![Uruchom stronę przepływu użytkownika w portalu z wyróżnionym przyciskiem Uruchom przepływ użytkownika](./media/add-sign-up-and-sign-in-policy/signup-signin-run-now.png)

1. Wprowadź prawidłowy adres e-mail, kliknij pozycję **Wyślij kod weryfikacyjny**, Wprowadź otrzymany kod weryfikacyjny, a następnie wybierz pozycję **Weryfikuj kod**.
1. Wprowadź nowe hasło i Potwierdź hasło.
1. Wybierz swój kraj i region, wprowadź nazwę, która ma zostać wyświetlona, wprowadź kod pocztowy, a następnie kliknij przycisk **Utwórz**. Token jest zwracany do `https://jwt.ms` i powinien być wyświetlony.
1. Teraz możesz ponownie uruchomić przepływ użytkownika i móc zalogować się przy użyciu utworzonego konta. Zwrócony token obejmuje oświadczenia wybrane w kraju/regionie, nazwie i kodzie pocztowym.

> [!NOTE]
> Środowisko "Uruchamianie przepływu użytkownika" nie jest obecnie zgodne z typem adresu URL odpowiedzi SPA przy użyciu przepływu kodu autoryzacji. Aby użyć środowiska "Uruchamianie przepływu użytkownika" z tego rodzaju aplikacjami, należy zarejestrować adres URL odpowiedzi typu "Web" i włączyć przepływ niejawny zgodnie z opisem w [tym miejscu](tutorial-register-spa.md).

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-sign-up-and-sign-in-policy"></a>Tworzenie zasad tworzenia konta i logowania

Zasady niestandardowe są zestawem plików XML przekazywanym do dzierżawy Azure AD B2C w celu zdefiniowania podróży użytkowników. Udostępniamy pakiety początkowe z kilkoma wstępnie utworzonymi zasadami, takimi jak: rejestrowanie i logowanie, resetowanie haseł i zasady edytowania profilu. Aby uzyskać więcej informacji, zobacz Wprowadzenie [do zasad niestandardowych w Azure AD B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy).

::: zone-end

## <a name="next-steps"></a>Następne kroki

* Dodaj [Logowanie przy użyciu dostawcy tożsamości społecznościowej](add-identity-provider.md).
* Skonfiguruj [przepływ resetowania hasła](add-password-reset-policy.md).
