---
title: Jak zarządzać hasłami aplikacji — Azure Active Directory | Microsoft Docs
description: Dowiedz się więcej o hasłach aplikacji i sposobach ich użycia w odniesieniu do weryfikacji dwuetapowej.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: richagi
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 05/28/2020
ms.author: curtand
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: c9d8bb85aac87503ca641f6633668a6dc582882f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90056085"
---
# <a name="manage-app-passwords-for-two-step-verification"></a>Zarządzanie hasłami aplikacji w celu weryfikacji dwuetapowej

>[!Important]
>Administrator może nie zezwalać na korzystanie z haseł aplikacji. Jeśli nie widzisz **haseł aplikacji** jako opcji, nie są one dostępne w Twojej organizacji.

W przypadku korzystania z haseł aplikacji ważne jest, aby pamiętać:

- Hasła aplikacji są generowane automatycznie i należy je utworzyć i wprowadzić raz na aplikację.

- Dla każdego użytkownika istnieje limit 40 haseł. Jeśli spróbujesz utworzyć jeden po tym limicie, zostanie wyświetlony monit o usunięcie istniejącego hasła przed utworzeniem nowego.

    >[!Note]
    >Klienci korzystający z pakietu Office 2013 (w tym Outlook) obsługują nowe protokoły uwierzytelniania i mogą być używani do weryfikacji dwuetapowej. Ta pomoc techniczna oznacza, że po włączeniu weryfikacji dwuetapowej nie będą już potrzebne hasła aplikacji dla klientów pakietu Office 2013. Aby uzyskać więcej informacji, zobacz artykuł [jak nowoczesne uwierzytelnianie działa dla pakietów office 2013 i office 2016 Client Apps](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517) .

## <a name="create-new-app-passwords"></a>Utwórz nowe hasła aplikacji

Podczas początkowego procesu rejestracji dwuetapowej jest dostępne jedno hasło aplikacji. Jeśli potrzebujesz więcej niż jednego, musisz utworzyć je samodzielnie. Hasła aplikacji można tworzyć z wielu obszarów, w zależności od konfiguracji weryfikacji dwuskładnikowej w organizacji. Aby uzyskać więcej informacji na temat rejestrowania się w celu korzystania z weryfikacji dwuetapowej przy użyciu konta służbowego, zobacz [Omówienie weryfikacji dwuskładnikowej oraz konta służbowego](multi-factor-authentication-end-user-first-time.md) i powiązane z nim artykuły.

### <a name="where-to-create-and-delete-your-app-passwords"></a>Gdzie można tworzyć i usuwać hasła aplikacji

Hasła aplikacji można tworzyć i usuwać w zależności od sposobu korzystania z weryfikacji dwuskładnikowej:

- **Twoja organizacja używa weryfikacji dwuskładnikowej i dodatkowej strony weryfikacji zabezpieczeń.** Jeśli używasz konta służbowego (np alain@contoso.com .) w ramach weryfikacji dwuskładnikowej w organizacji, możesz zarządzać hasłami aplikacji na [stronie dodatkowej weryfikacji zabezpieczeń](https://account.activedirectory.windowsazure.com/Proofup.aspx). Aby uzyskać szczegółowe instrukcje, zobacz [Tworzenie i usuwanie haseł aplikacji przy użyciu strony dodatkowej weryfikacji zabezpieczeń](#create-and-delete-app-passwords-from-the-additional-security-verification-page) w tym artykule.

- **Twoja organizacja używa weryfikacji dwuskładnikowej i portalu Office 365.** Jeśli używasz konta służbowego (takiego jak, alain@contoso.com ), weryfikacji dwuskładnikowej i Microsoft 365 aplikacji w organizacji, możesz zarządzać hasłami aplikacji ze [strony portalu pakietu Office 365](https://www.office.com). Aby uzyskać szczegółowe instrukcje, zobacz [Tworzenie i usuwanie haseł aplikacji przy użyciu portalu Office 365](#create-and-delete-app-passwords-using-the-office-365-portal) w tym artykule.

- **Korzystasz z weryfikacji dwuetapowej z konto Microsoft osobistym.** Jeśli używasz osobistego konto Microsoft (na przykład, alain@outlook.com ) z weryfikacją dwuskładnikową, możesz zarządzać hasłami aplikacji ze [strony podstawy zabezpieczeń](https://account.microsoft.com/security/). Aby uzyskać szczegółowe instrukcje, zobacz [Używanie haseł aplikacji z aplikacjami, które nie obsługują weryfikacji dwuetapowej](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-and-two-step-verification).

## <a name="create-and-delete-app-passwords-from-the-additional-security-verification-page"></a>Tworzenie i usuwanie haseł aplikacji ze strony dodatkowej weryfikacji zabezpieczeń

Hasła aplikacji można tworzyć i usuwać ze strony **dodatkowej weryfikacji zabezpieczeń** dla konta służbowego.

1. Zaloguj się do [strony dodatkowa Weryfikacja zabezpieczeń](https://account.activedirectory.windowsazure.com/Proofup.aspx), a następnie wybierz pozycję **hasła aplikacji**.

    ![Strona hasła aplikacji z wyróżnioną kartą hasła aplikacji](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page.png)

2. Wybierz pozycję **Utwórz**, wpisz nazwę aplikacji, która wymaga hasła aplikacji, a następnie wybierz przycisk **dalej**.

    ![Strona tworzenie haseł aplikacji z nazwą aplikacji, która wymaga hasła](media/multi-factor-authentication-end-user-app-passwords/mfa-create-app-password-page.png)

3. Skopiuj hasło ze strony **hasło aplikacji** , a następnie wybierz pozycję **Zamknij**.

    ![Strona hasła aplikacji z hasłem dla określonej aplikacji](media/multi-factor-authentication-end-user-app-passwords/mfa-your-app-password-page.png)

4. Na stronie **hasła aplikacji** upewnij się, że Twoja aplikacja jest wyświetlana na liście.

     ![Strona haseł aplikacji z nową aplikacją wyświetlaną na liście](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-with-new-password.png)  

5. Otwórz aplikację, dla której zostało utworzone hasło aplikacji (na przykład Outlook 2010), a następnie wklej hasło aplikacji po wyświetleniu monitu. Należy to zrobić tylko raz dla każdej aplikacji.

### <a name="to-delete-an-app-password-using-the-app-passwords-page"></a>Aby usunąć hasło aplikacji przy użyciu strony haseł aplikacji

1. Na stronie **hasła aplikacji** wybierz pozycję **Usuń** obok hasła aplikacji, które chcesz usunąć.

   ![Usuwanie hasła aplikacji](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-delete.png)

2. Wybierz pozycję **tak** , aby potwierdzić, że chcesz usunąć hasło, a następnie wybierz pozycję **Zamknij**.

    Pomyślnie usunięto hasło aplikacji.

## <a name="create-and-delete-app-passwords-using-the-office-365-portal"></a>Tworzenie i usuwanie haseł aplikacji przy użyciu portalu pakietu Office 365

Jeśli korzystasz z weryfikacji dwuetapowej przy użyciu konta służbowego i aplikacji Microsoft 365, możesz tworzyć i usuwać hasła aplikacji przy użyciu portalu pakietu Office 365.

### <a name="to-create-app-passwords-using-the-office-365-portal"></a>Aby utworzyć hasła aplikacji przy użyciu portalu pakietu Office 365

1. Zaloguj się do swojego konta służbowego, przejdź do [strony Moje konto](https://portal.office.com), wybierz pozycję **zabezpieczenia & prywatność**, a następnie rozwiń węzeł **dodatkowa Weryfikacja zabezpieczeń**.

    ![Portal pakietu Office przedstawiający rozwinięty obszar dodatkowej weryfikacji zabezpieczeń](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-o365-my-account-page.png)

2. Zaznacz wyświetlany tekst, **Utwórz i Zarządzaj hasłami aplikacji** , aby otworzyć stronę **hasła aplikacji** .

    ![Strona hasła aplikacji z wyróżnioną kartą hasła aplikacji](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page.png)

3. Wybierz pozycję **Utwórz**, wpisz nazwę aplikacji, która wymaga hasła aplikacji, a następnie wybierz przycisk **dalej**.

    ![Strona tworzenie haseł aplikacji z nazwą aplikacji, która wymaga hasła](media/multi-factor-authentication-end-user-app-passwords/mfa-create-app-password-page.png)

4. Skopiuj hasło ze strony **hasło aplikacji** , a następnie wybierz pozycję **Zamknij**.

    ![Strona hasła aplikacji z hasłem dla określonej aplikacji](media/multi-factor-authentication-end-user-app-passwords/mfa-your-app-password-page.png)

5. Na stronie **hasła aplikacji** upewnij się, że Twoja aplikacja jest wyświetlana na liście.

     ![Strona haseł aplikacji z nową aplikacją wyświetlaną na liście](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-with-new-password.png)  

6. Otwórz aplikację, dla której zostało utworzone hasło aplikacji (na przykład Outlook 2010), a następnie wklej hasło aplikacji po wyświetleniu monitu. Należy to zrobić tylko raz dla każdej aplikacji.

### <a name="to-delete-app-passwords-using-the-app-passwords-page"></a>Aby usunąć hasła aplikacji przy użyciu strony haseł aplikacji

1. Na stronie **hasła aplikacji** wybierz pozycję **Usuń** obok hasła aplikacji, które chcesz usunąć.

   ![Usuwanie hasła aplikacji](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-delete.png)

2. W polu potwierdzenia wybierz pozycję **tak** , a następnie wybierz pozycję **Zamknij**.

    Pomyślnie usunięto hasło aplikacji.

## <a name="if-your-app-passwords-arent-working-properly"></a>Jeśli hasła aplikacji nie działają prawidłowo

Upewnij się, że hasło zostało wpisane prawidłowo. Jeśli masz pewność, że hasło zostało wprowadzone poprawnie, możesz spróbować ponownie zalogować się i utworzyć nowe hasło aplikacji. Jeśli żadna z tych opcji nie rozwiąże problemu, skontaktuj się z działem pomocy technicznej Twojej organizacji, aby można było usunąć istniejące hasła aplikacji, co pozwoli Ci utworzyć nowe marki.

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie ustawieniami weryfikacji dwuetapowej](multi-factor-authentication-end-user-manage-settings.md)

- Wypróbuj [aplikację Microsoft Authenticator](user-help-auth-app-download-install.md) , aby zweryfikować logowania przy użyciu powiadomień aplikacji zamiast odbierać dokumenty lub wywołania.
