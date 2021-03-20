---
title: Włączanie uwierzytelniania wieloskładnikowego w usłudze Azure AD
description: W tym samouczku dowiesz się, jak włączyć Multi-Factor Authentication usługi Azure AD dla grupy użytkowników i przetestować monit pomocniczy w trakcie zdarzenia logowania.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: justinha
author: justinha
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2e8bf2ccbf7a53563013c7ba653a6f6e8905337
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97881314"
---
# <a name="tutorial-secure-user-sign-in-events-with-azure-ad-multi-factor-authentication"></a>Samouczek: Zabezpieczanie zdarzeń logowania użytkowników za pomocą usługi Azure AD Multi-Factor Authentication

Uwierzytelnianie wieloskładnikowe (MFA) to proces, w którym użytkownik jest monitowany podczas logowania w celu uzyskania dodatkowych form identyfikacji. Ten monit może być wprowadzeniem kodu w Cellphone lub w celu przeskanowania odcisku palca. Gdy wymagana jest druga forma uwierzytelniania, zabezpieczenia są zwiększane, ponieważ ten dodatkowy czynnik nie jest czymś, co jest łatwe do uzyskania lub zduplikowania przez osobę atakującą.

Usługa Azure AD Multi-Factor Authentication i zasady dostępu warunkowego umożliwiają elastyczne Włączanie uwierzytelniania wieloskładnikowego dla użytkowników podczas określonych zdarzeń logowania. Oto [film wideo dotyczący konfigurowania i wymuszania uwierzytelniania wieloskładnikowego w dzierżawie](https://www.youtube.com/watch?v=qNndxl7gqVM) (**zalecane**)

> [!IMPORTANT]
> W tym samouczku pokazano, jak włączyć usługę Azure AD Multi-Factor Authentication.
>
> Jeśli Twój zespół IT nie włączył możliwości korzystania z usługi Azure AD Multi-Factor Authentication lub masz problemy podczas logowania, skontaktuj się z pomocą techniczną, aby uzyskać dodatkową pomoc.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie zasad dostępu warunkowego w celu włączenia Multi-Factor Authentication usługi Azure AD dla grupy użytkowników
> * Skonfiguruj warunki zasad, które monitują o usługę MFA
> * Przetestuj proces MFA jako użytkownik

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka potrzebne są następujące zasoby i uprawnienia:

* Działająca dzierżawa usługi Azure AD z co najmniej Azure AD — wersja Premium włączoną licencją P1 lub próbną.
    * W razie potrzeby [Utwórz je bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Konto z uprawnieniami *administratora globalnego* .
* Użytkownik niebędący administratorem z hasłem znanym, takim jak *Użytkownik testowy*. W tym samouczku można testować środowisko Multi-Factor Authentication usługi Azure AD dla użytkowników końcowych za pomocą tego konta.
    * Jeśli musisz utworzyć użytkownika, zobacz [Szybki Start: Dodawanie nowych użytkowników do Azure Active Directory](../fundamentals/add-users-azure-active-directory.md).
* Grupa, do której należy użytkownik niebędący administratorem, na przykład *MFA-test-Group*. W tym samouczku włączysz Multi-Factor Authentication usługi Azure AD dla tej grupy.
    * Jeśli musisz utworzyć grupę, zobacz jak [utworzyć grupę i dodać członków w Azure Active Directory](../fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="create-a-conditional-access-policy"></a>Tworzenie zasad dostępu warunkowego

Zalecanym sposobem włączania i używania usługi Azure AD Multi-Factor Authentication są zasady dostępu warunkowego. Dostęp warunkowy pozwala tworzyć i definiować zasady reagujące na zdarzenia logowania i żądać dodatkowych akcji, zanim użytkownik uzyska dostęp do aplikacji lub usługi.

![Diagram omówienia sposobu działania dostępu warunkowego w celu zabezpieczenia procesu logowania](media/tutorial-enable-azure-mfa/conditional-access-overview.png)

Zasady dostępu warunkowego mogą być szczegółowe i szczegółowe, a celem jest umożliwienie użytkownikom wydajnej pracy wszędzie tam, gdzie jest to możliwe, ale również ochrona organizacji. W tym samouczku utworzysz podstawowe zasady dostępu warunkowego, aby monitować usługę MFA, gdy użytkownik zaloguje się do Azure Portal. W kolejnym samouczku w tej serii można skonfigurować usługę Azure AD Multi-Factor Authentication przy użyciu zasad dostępu warunkowego opartego na ryzyku.

Najpierw utwórz zasady dostępu warunkowego i Przypisz grupę testową użytkowników w następujący sposób:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta z uprawnieniami *administratora globalnego* .
1. Wyszukaj i wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **zabezpieczenia** z menu po lewej stronie.
1. Wybierz pozycję **dostęp warunkowy**, a następnie wybierz pozycję **+ nowe zasady**.
1. Wprowadź nazwę zasad, na przykład *pilotażowa funkcja MFA*.
1. W obszarze **przypisania** wybierz pozycję **Użytkownicy i grupy**, a następnie przycisk radiowy **Wybierz użytkowników i grupy** .
1. Zaznacz pole wyboru **Użytkownicy i grupy**, a następnie **Wybierz opcję** przeglądania dostępnych użytkowników i grup usługi Azure AD.
1. Wyszukaj i wybierz grupę usługi Azure AD, taką jak *MFA-test-Group*, a następnie wybierz **pozycję Wybierz**.

    [![Wybierz grupę usługi Azure AD, która ma być używana z zasadami ](media/tutorial-enable-azure-mfa/select-group-for-conditional-access-cropped.png) dostępu warunkowego](media/tutorial-enable-azure-mfa/select-group-for-conditional-access.png#lightbox)

1. Aby zastosować zasady dostępu warunkowego dla grupy, wybierz pozycję **gotowe**.

## <a name="configure-the-conditions-for-multi-factor-authentication"></a>Skonfiguruj warunki uwierzytelniania wieloskładnikowego

W przypadku tworzenia zasad dostępu warunkowego i grupy testowej przypisanej przez użytkowników można teraz definiować aplikacje w chmurze lub akcje wyzwalające zasady. Te aplikacje lub akcje w chmurze to scenariusze, które należy podjąć, aby wymagać dodatkowego przetwarzania, na przykład w celu wyświetlenia monitu o usługę MFA. Na przykład można zdecydować, że dostęp do aplikacji finansowej lub korzystania z narzędzi do zarządzania wymaga jako dodatkowy monit weryfikacyjny.

W tym samouczku Skonfiguruj zasady dostępu warunkowego, aby wymagać uwierzytelniania wieloskładnikowego, gdy użytkownik zaloguje się do Azure Portal.

1. Wybierz pozycję **Aplikacje w chmurze lub akcje**. Możesz zastosować zasady dostępu warunkowego do *wszystkich aplikacji w chmurze* lub *wybranych aplikacji*. Aby zapewnić elastyczność, można również wykluczyć niektóre aplikacje z zasad.

    Na potrzeby tego samouczka na stronie *dołączania* wybierz przycisk radiowy **Wybierz aplikacje** .

1. Wybierz pozycję **Wybierz**, a następnie Przeglądaj listę dostępnych zdarzeń logowania, których można użyć.

    Na potrzeby tego samouczka wybierz **Microsoft Azure zarządzanie** , aby zasady dotyczyły zdarzeń logowania do Azure Portal.

1. Aby zastosować wybrane aplikacje, wybierz **pozycję Wybierz**, a następnie pozycję **gotowe**.

    ![Wybierz aplikację do zarządzania Microsoft Azure, która ma zostać uwzględniona w zasadach dostępu warunkowego](media/tutorial-enable-azure-mfa/select-azure-management-app.png)

Kontrola dostępu pozwala określić wymagania dla użytkownika, które mają mieć przyznane uprawnienia, takie jak wymaganie zatwierdzonej aplikacji klienckiej lub użycie urządzenia, które jest dołączone do hybrydowej usługi Azure AD. W tym samouczku skonfiguruj kontrolę dostępu, aby wymagać uwierzytelniania wieloskładnikowego w trakcie zdarzenia logowania do Azure Portal.

1. W obszarze *kontrole dostępu* wybierz pozycję **Udziel**, a następnie upewnij się, że wybrano przycisk radiowy **Udziel dostępu** .
1. Zaznacz pole wyboru **Wymagaj uwierzytelniania wieloskładnikowego**, a następnie wybierz **pozycję Wybierz**.

Zasady dostępu warunkowego można ustawić na wartość *raport — tylko* wtedy, gdy chcesz zobaczyć, w jaki sposób konfiguracja będzie miała wpływ na użytkowników, lub *wyłączyć* , jeśli nie chcesz teraz korzystać z zasad. Grupa testowa użytkowników przeznaczona dla tego samouczka umożliwia włączenie zasad i przetestowanie Multi-Factor Authentication usługi Azure AD.

1. Ustaw przełącznik *Włącz zasady* na wartość **włączone**.
1. Aby zastosować zasady dostępu warunkowego, wybierz pozycję **Utwórz**.

## <a name="test-azure-ad-multi-factor-authentication"></a>Testowanie Multi-Factor Authentication usługi Azure AD

Zobaczmy w działaniu zasady dostępu warunkowego i usługi Azure AD Multi-Factor Authentication. Najpierw Zaloguj się do zasobu, który nie wymaga uwierzytelniania MFA w następujący sposób:

1. Otwórz nowe okno przeglądarki w trybie InPrivate lub incognito i przejdź do [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)
1. Zaloguj się przy użyciu użytkownika testowego niebędącego administratorem, takiego jak *Użytkownik testowy*. Nie ma monitu o ukończenie uwierzytelniania MFA.
1. Zamknij okno przeglądarki.

Teraz Zaloguj się do Azure Portal. Ponieważ Azure Portal została skonfigurowana w zasadach dostępu warunkowego, aby wymagać dodatkowej weryfikacji, otrzymasz monit Multi-Factor Authentication usługi Azure AD.

1. Otwórz nowe okno przeglądarki w trybie InPrivate lub incognito i przejdź do [https://portal.azure.com](https://portal.azure.com) .
1. Zaloguj się przy użyciu użytkownika testowego niebędącego administratorem, takiego jak *Użytkownik testowy*. Musisz zarejestrować się w usłudze i korzystać z usługi Azure AD Multi-Factor Authentication. Postępuj zgodnie z monitami, aby zakończyć proces i zweryfikować pomyślne zalogowanie się do Azure Portal.

    ![Postępuj zgodnie z monitami przeglądarki, a następnie zaloguj się przy użyciu monitu dotyczącego uwierzytelniania wieloskładnikowego, aby się zalogować](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

1. Zamknij okno przeglądarki.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz już używać zasad dostępu warunkowego do włączania usługi Azure AD Multi-Factor Authentication skonfigurowanej w ramach tego samouczka, Usuń zasady, wykonując następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wyszukaj i wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **zabezpieczenia** z menu po lewej stronie.
1. Wybierz pozycję **dostęp warunkowy**, a następnie wybierz utworzone zasady, takie jak usługa *MFA pilotażowa*
1. Wybierz pozycję **Usuń**, a następnie potwierdź, że chcesz usunąć zasady.

## <a name="next-steps"></a>Następne kroki

W tym samouczku włączono usługę Azure AD Multi-Factor Authentication przy użyciu zasad dostępu warunkowego dla wybranej grupy użytkowników. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie zasad dostępu warunkowego w celu włączenia usługi Azure AD Multi-Factor Authentication dla grupy użytkowników usługi Azure AD
> * Skonfiguruj warunki zasad, które monitują o usługę MFA
> * Przetestuj proces MFA jako użytkownik

> [!div class="nextstepaction"]
> [Włącz funkcję zapisywania zwrotnego haseł do samoobsługowego resetowania hasła (SSPR)](./tutorial-enable-sspr-writeback.md)
