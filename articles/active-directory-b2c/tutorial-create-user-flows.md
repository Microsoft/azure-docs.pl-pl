---
title: Samouczek — Tworzenie przepływów użytkowników — Azure Active Directory B2C
description: Dowiedz się, jak tworzyć przepływy użytkowników w Azure Portal, aby włączyć rejestrowanie, logowanie i edytowanie profilu użytkownika dla aplikacji w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 07/01/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cc6673615c85b34975d6743da6da88ca841bcf35
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87005365"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>Samouczek: Tworzenie przepływów użytkowników w Azure Active Directory B2C

W aplikacjach mogą istnieć [przepływy użytkowników](user-flow-overview.md) , które umożliwiają użytkownikom rejestrowanie się, logowanie lub Zarządzanie profilem. Możesz tworzyć wiele przepływów użytkowników różnych typów w dzierżawie Azure Active Directory B2C (Azure AD B2C) i używać ich w aplikacjach zgodnie z potrzebami. Przepływy użytkowników mogą być ponownie używane między aplikacjami.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Tworzenie przepływu użytkownika dotyczącego rejestracji i logowania
> * Tworzenie przepływu użytkownika dotyczącego edytowania profilu
> * Tworzenie przepływu użytkownika dotyczącego resetowania haseł

W tym samouczku przedstawiono sposób tworzenia niektórych zalecanych przepływów użytkowników przy użyciu Azure Portal. Jeśli szukasz informacji o sposobie konfigurowania przepływu poświadczeń hasła właściciela zasobu (ROPC) w aplikacji, zobacz [Konfigurowanie przepływu poświadczeń hasła właściciela zasobu w Azure AD B2C](configure-ropc.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

[Zarejestruj aplikacje](tutorial-register-applications.md) , które są częścią przepływów użytkowników, które chcesz utworzyć.

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Tworzenie przepływu użytkownika dotyczącego rejestracji i logowania

Przepływ użytkowników rejestracji i logowania obsługuje zarówno środowisko tworzenia konta, jak i logowania przy użyciu jednej konfiguracji. Użytkownicy Twojej aplikacji nie wyłączają właściwej ścieżki w zależności od kontekstu.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Na pasku narzędzi portalu wybierz ikonę **katalog i subskrypcję** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.

    ![B2C dzierżawy, katalogu i subskrypcji, Azure Portal](./media/tutorial-create-user-flows/directory-subscription-pane.png)

1. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. W obszarze **zasady**wybierz pozycję **przepływy użytkowników**, a następnie wybierz pozycję **Nowy przepływ użytkownika**.

    ![Strona przepływy użytkownika w portalu z wyróżnionym przyciskiem nowy przepływ użytkownika](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

1. Na karcie **zalecane** wybierz przepływ użytkowników **rejestracja i logowanie** .

    ![Wybierz stronę przepływu użytkownika z wyróżnioną pozycją Zarejestruj się i zaloguj się](./media/tutorial-create-user-flows/signup-signin-type.png)

1. Wprowadź **nazwę** przepływu użytkownika. Na przykład *signupsignin1*.
1. W przypadku **dostawców tożsamości**wybierz pozycję **rejestracja w wiadomości e-mail**.

    ![Utwórz stronę przepływu użytkownika w Azure Portal z wyróżnionymi właściwościami](./media/tutorial-create-user-flows/signup-signin-properties.png)

1. W polu **atrybuty użytkownika i oświadczenia**wybierz oświadczenia i atrybuty, które mają być zbierane i wysyłane przez użytkownika podczas tworzenia konta. Na przykład wybierz pozycję **Pokaż więcej**, a następnie wybierz atrybuty i oświadczenia dla **kraju/regionu**, **Nazwa wyświetlana**i **Kod pocztowy**. Kliknij przycisk **OK**.

    ![Strona wyboru atrybutów i oświadczeń z trzema wybranymi oświadczeniami](./media/tutorial-create-user-flows/signup-signin-attributes.png)

1. Kliknij przycisk **Utwórz** , aby dodać przepływ użytkownika. Prefiks *B2C_1* jest automatycznie dołączany do nazwy.

### <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

1. Wybierz utworzony przepływ użytkownika, aby otworzyć jego stronę przeglądu, a następnie wybierz pozycję **Uruchom przepływ użytkownika**.
1. W przypadku **aplikacji**wybierz aplikację sieci Web o nazwie *webapp1* , która została wcześniej zarejestrowana. Powinien być pokazywany **adres URL odpowiedzi** `https://jwt.ms` .
1. Kliknij pozycję **Uruchom przepływ użytkownika**, a następnie wybierz pozycję **Utwórz konto teraz**.

    ![Uruchom stronę przepływu użytkownika w portalu z wyróżnionym przyciskiem Uruchom przepływ użytkownika](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. Wprowadź prawidłowy adres e-mail, kliknij pozycję **Wyślij kod weryfikacyjny**, Wprowadź otrzymany kod weryfikacyjny, a następnie wybierz pozycję **Weryfikuj kod**.
1. Wprowadź nowe hasło i Potwierdź hasło.
1. Wybierz swój kraj i region, wprowadź nazwę, która ma zostać wyświetlona, wprowadź kod pocztowy, a następnie kliknij przycisk **Utwórz**. Token jest zwracany do `https://jwt.ms` i powinien być wyświetlony.
1. Teraz możesz ponownie uruchomić przepływ użytkownika i móc zalogować się przy użyciu utworzonego konta. Zwrócony token obejmuje oświadczenia wybrane w kraju/regionie, nazwie i kodzie pocztowym.

## <a name="create-a-profile-editing-user-flow"></a>Tworzenie przepływu użytkownika dotyczącego edytowania profilu

Jeśli chcesz umożliwić użytkownikom edytowanie ich profilu w aplikacji, Użyj profilu użytkownika edytującego profil.

1. W menu strony Przegląd dzierżawy Azure AD B2C wybierz pozycję **przepływy użytkowników**, a następnie wybierz pozycję **Nowy przepływ użytkownika**.
1. Wybierz pozycję **Edytowanie profilu** przepływ użytkownika na karcie **zalecane** .
1. Wprowadź **nazwę** przepływu użytkownika. Na przykład *profileediting1*.
1. W przypadku **dostawców tożsamości**wybierz opcję **Logowanie do konta lokalnego**.
1. W polu **atrybuty użytkownika**wybierz atrybuty, które mają być edytowane przez klienta w swoim profilu. Na przykład wybierz pozycję **Pokaż więcej**, a następnie wybierz zarówno atrybuty, **jak i oświadczenia**dotyczące **nazwy wyświetlanej** i stanowiska. Kliknij przycisk **OK**.
1. Kliknij przycisk **Utwórz** , aby dodać przepływ użytkownika. Prefiks *B2C_1* jest automatycznie dołączany do nazwy.

### <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

1. Wybierz utworzony przepływ użytkownika, aby otworzyć jego stronę przeglądu, a następnie wybierz pozycję **Uruchom przepływ użytkownika**.
1. W przypadku **aplikacji**wybierz aplikację sieci Web o nazwie *webapp1* , która została wcześniej zarejestrowana. Powinien być pokazywany **adres URL odpowiedzi** `https://jwt.ms` .
1. Kliknij pozycję **Uruchom przepływ użytkownika**, a następnie zaloguj się przy użyciu utworzonego wcześniej konta.
1. Teraz można zmienić nazwę wyświetlaną i stanowisko użytkownika. Kliknij pozycję **Kontynuuj**. Token jest zwracany do `https://jwt.ms` i powinien być wyświetlony.

## <a name="create-a-password-reset-user-flow"></a>Tworzenie przepływu użytkownika dotyczącego resetowania haseł

Aby umożliwić użytkownikom aplikacji Resetowanie hasła, należy użyć przepływu użytkownika resetowania hasła.

1. W menu przegląd Azure AD B2C dzierżawy wybierz pozycję **przepływy użytkowników**, a następnie wybierz pozycję **Nowy przepływ użytkownika**.
1. Wybierz przepływ użytkownika **resetowania hasła** na karcie **zalecane** .
1. Wprowadź **nazwę** przepływu użytkownika. Na przykład *passwordreset1*.
1. W przypadku **dostawców tożsamości**Włącz **Resetowanie hasła przy użyciu adresu e-mail**.
1. W obszarze oświadczenia aplikacji kliknij przycisk **Pokaż więcej** i wybierz oświadczenia, które mają być zwracane w tokenach autoryzacji wysyłanych z powrotem do aplikacji. Na przykład wybierz pozycję **Identyfikator obiektu użytkownika**.
1. Kliknij przycisk **OK**.
1. Kliknij przycisk **Utwórz** , aby dodać przepływ użytkownika. Prefiks *B2C_1* jest automatycznie dołączany do nazwy.

### <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

1. Wybierz utworzony przepływ użytkownika, aby otworzyć jego stronę przeglądu, a następnie wybierz pozycję **Uruchom przepływ użytkownika**.
1. W przypadku **aplikacji**wybierz aplikację sieci Web o nazwie *webapp1* , która została wcześniej zarejestrowana. Powinien być pokazywany **adres URL odpowiedzi** `https://jwt.ms` .
1. Kliknij pozycję **Uruchom przepływ użytkownika**, Sprawdź adres e-mail konta, które zostało utworzone wcześniej, a następnie wybierz pozycję **Kontynuuj**.
1. Masz teraz możliwość zmiany hasła dla użytkownika. Zmień hasło i wybierz pozycję **Kontynuuj**. Token jest zwracany do `https://jwt.ms` i powinien być wyświetlony.

## <a name="next-steps"></a>Następne kroki

W tym artykule zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie przepływu użytkownika dotyczącego rejestracji i logowania
> * Tworzenie przepływu użytkownika dotyczącego edytowania profilu
> * Tworzenie przepływu użytkownika dotyczącego resetowania haseł

Następnie Dowiedz się więcej o dodawaniu dostawców tożsamości do aplikacji, aby umożliwić użytkownikom logowanie się z dostawcami, takimi jak Azure AD, Amazon, Facebook, GitHub, LinkedIn, Microsoft lub Twitter.

> [!div class="nextstepaction"]
> [Dodawanie dostawców tożsamości do aplikacji >](tutorial-add-identity-providers.md)
