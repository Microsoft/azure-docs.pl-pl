---
title: Dodawanie kont innych niż Microsoft do aplikacji Microsoft Authenticator — Azure AD
description: Dodaj konta inne niż Microsoft, takie jak Google lub Facebook do aplikacji Microsoft Authenticator, aby zweryfikować swoją tożsamość przy użyciu weryfikacji dwuskładnikowej.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 01/24/2019
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 04076ed5b91f8e840a084b784477d07eea5adee7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87015974"
---
# <a name="add-non-microsoft-accounts-to-the-microsoft-authenticator-app"></a>Dodawanie kont innych niż Microsoft do aplikacji Microsoft Authenticator

Dodaj konta inne niż Microsoft, takie jak Google, Facebook lub GitHub, do aplikacji Microsoft Authenticator na potrzeby weryfikacji dwuskładnikowej. Aplikacja Microsoft Authenticator współdziała z dowolną aplikacją używającą weryfikacji dwuetapowej i dowolnym kontem obsługującym standardy TOTP (Time-based One-time Password).

>[!Important]
>Aby można było dodać konto, należy pobrać i zainstalować aplikację Microsoft Authenticator. Jeśli jeszcze tego nie zrobiono, wykonaj kroki opisane w artykule [pobieranie i instalowanie aplikacji](user-help-auth-app-download-install.md) .

## <a name="add-personal-accounts"></a>Dodawanie kont osobistych

Ogólnie rzecz biorąc, w przypadku wszystkich kont osobistych należy:

1. Zaloguj się do swojego konta, a następnie Włącz weryfikację dwuskładnikową przy użyciu urządzenia lub komputera.

2. Dodaj konto do aplikacji Microsoft Authenticator. Może pojawić się prośba o przeskanowanie kodu QR w ramach tego procesu.

    >[!Note]
    >Jeśli konfigurujesz aplikację Microsoft Authenticator po raz pierwszy, może zostać wyświetlony monit z pytaniem, czy zezwolić aplikacji na dostęp do aparatu (iOS), czy zezwolić aplikacji na wykonywanie zdjęć i nagrywanie wideo (Android). Musisz wybrać opcję **Zezwól** , aby aplikacja Authenticator mogła uzyskać dostęp do Twojego aparatu, aby w następnym kroku uzyskać zdjęcie kodu QR. Jeśli nie zezwolisz na korzystanie z aparatu, nadal możesz skonfigurować aplikację uwierzytelniającej, ale musisz ręcznie dodać informacje o kodzie. Aby dowiedzieć się, jak ręcznie dodać kod, zobacz temat [Ręczne dodawanie konta do aplikacji](user-help-auth-app-add-account-manual.md).

Udostępniamy ten proces dla kont w serwisach Facebook, Google, GitHub i Amazon, ale ten proces jest taki sam dla każdej innej aplikacji, takiej jak usługi Instagram, Netflix lub Adobe.

## <a name="add-your-google-account"></a>Dodawanie konta Google

Dodaj swoje konto Google, włączając weryfikację dwuskładnikową, a następnie dodając konto do aplikacji.

### <a name="turn-on-two-factor-verification"></a>Włącz weryfikację dwuskładnikową

1. Na komputerze przejdź do https://myaccount.google.com/signinoptions/two-step-verification/enroll-welcome , wybierz pozycję **Rozpocznij pracę**, a następnie zweryfikuj swoją tożsamość.

2. Postępuj zgodnie z instrukcjami na stronie, aby włączyć weryfikację dwuetapową dla osobistego konta Google.

### <a name="add-your-google-account-to-the-app"></a>Dodawanie konta Google do aplikacji

1. Na stronie zabezpieczenia konta Google na komputerze ( https://myaccount.google.com/security) Przejdź do sekcji **Dodawanie kolejnych kroków w celu zweryfikowania** , wybierz pozycję **Konfiguruj** z poziomu **aplikacji uwierzytelniania** ).

2. Na stronie **Pobierz kody z aplikacji Authenticator** wybierz pozycję **Android** lub **iPhone** na podstawie typu telefonu, a następnie wybierz przycisk **dalej**.

    Otrzymujesz kod QR, którego można użyć do automatycznego kojarzenia konta z aplikacją Microsoft Authenticator. Nie zamykaj tego okna.

3. Otwórz aplikację Microsoft Authenticator, wybierz pozycję **Dodaj konto** z ikony **Dostosowywanie i kontrola** w prawym górnym rogu, a następnie wybierz pozycję **inne konto (Google, Facebook itp.)**.

4. Za pomocą aparatu urządzenia Zeskanuj kod QR na stronie **Konfigurowanie uwierzytelniania** na komputerze.

    >[!Note]
    >Jeśli aparat nie działa prawidłowo, można ręcznie wprowadzić kod QR i adres URL.

5. Przejrzyj stronę **kont** aplikacji Microsoft Authenticator na urządzeniu, aby upewnić się, że informacje o koncie są prawidłowe i że istnieje skojarzony sześciocyfrowy kod weryfikacyjny.

    W celu zapewnienia dodatkowych zabezpieczeń kod weryfikacyjny zmienia się co 30 sekund, uniemożliwiając komuś wielokrotne użycie kodu.

6. Wybierz pozycję **dalej** na stronie **Konfigurowanie uwierzytelniania** na komputerze, wpisz sześciocyfrowy kod weryfikacyjny podany w aplikacji dla konta Google, a następnie wybierz pozycję **Weryfikuj**.

7. Twoje konto zostało zweryfikowane i możesz wybrać pozycję **gotowe** , aby zamknąć stronę **Konfigurowanie uwierzytelniania wystawcy** .

    >[!NOTE]
    >Aby uzyskać więcej informacji na temat weryfikacji dwuskładnikowej i konta Google, zobacz temat [Włączanie weryfikacji 2-etapowej](https://support.google.com/accounts/answer/185839) i [Dowiedz się więcej o weryfikacji dwuetapowej](https://www.google.com/landing/2step/help.html).

## <a name="add-your-facebook-account"></a>Dodawanie konta w serwisie Facebook

Dodaj swoje konto w usłudze Facebook, włączając weryfikację dwuskładnikową, a następnie dodając konto do aplikacji.

### <a name="turn-on-two-factor-verification"></a>Włącz weryfikację dwuskładnikową

1. Na komputerze otwórz serwis Facebook, wybierz menu rozwijane w prawym górnym rogu, a następnie przejdź do pozycji **Ustawienia**  >  **zabezpieczenia i zaloguj**się.

    Zostanie wyświetlona strona **zabezpieczenia i logowanie** .

2. Przejdź do opcji **Użyj uwierzytelniania dwuskładnikowego** w sekcji **uwierzytelnianie dwuskładnikowe** , a następnie wybierz pozycję **Edytuj**.

    Zostanie wyświetlona strona **uwierzytelnianie dwuskładnikowe** .

3. Wybierz pozycję **Włącz**.

### <a name="add-your-facebook-account-to-the-app"></a>Dodawanie konta w serwisie Facebook do aplikacji

1. Na stronie Facebook na komputerze przejdź do sekcji **Dodawanie kopii zapasowej** , a następnie wybierz pozycję **Instalator** w obszarze **aplikacja uwierzytelniania** .

    Otrzymujesz kod QR, którego można użyć do automatycznego kojarzenia konta z aplikacją Microsoft Authenticator. Nie zamykaj tego okna.

2. Otwórz aplikację Microsoft Authenticator, wybierz pozycję **Dodaj konto** z ikony **Dostosowywanie i kontrola** w prawym górnym rogu, a następnie wybierz pozycję **inne konto (Google, Facebook itp.)**.

3. Użyj aparatu urządzenia, aby zeskanować kod QR ze strony **uwierzytelniania dwuetapowego** na komputerze.

    >[!Note]
    >Jeśli aparat nie działa prawidłowo, można ręcznie wprowadzić kod QR i adres URL.

4. Przejrzyj stronę **kont** aplikacji Microsoft Authenticator na urządzeniu, aby upewnić się, że informacje o koncie są prawidłowe i że istnieje skojarzony sześciocyfrowy kod weryfikacyjny.

    W celu zapewnienia dodatkowych zabezpieczeń kod weryfikacyjny zmienia się co 30 sekund, uniemożliwiając komuś wielokrotne użycie kodu.

5. Wybierz pozycję **dalej** na stronie **uwierzytelniania dwuskładnikowego** na komputerze, a następnie wpisz sześciocyfrowy kod weryfikacyjny podany w aplikacji dla Twojego konta w serwisie Facebook.

    Twoje konto zostało zweryfikowane i możesz teraz użyć aplikacji, aby zweryfikować swoje konto.

    >[!NOTE]
    >Aby uzyskać więcej informacji na temat weryfikacji dwuskładnikowej i konta w serwisie Facebook, zobacz [co to jest uwierzytelnianie dwuskładnikowe i jak to działa?](https://www.facebook.com/help/148233965247823).

## <a name="add-your-github-account"></a>Dodawanie konta usługi GitHub

Dodaj konto usługi GitHub, włączając weryfikację dwuskładnikową, a następnie dodając konto do aplikacji.

### <a name="turn-on-two-factor-verification"></a>Włącz weryfikację dwuskładnikową

1. Na komputerze otwórz witrynę GitHub, wybierz swój obraz z prawego górnego rogu, a następnie wybierz pozycję **Ustawienia**.

    Zostanie wyświetlona strona **uwierzytelnianie dwuskładnikowe** .

2. Na pasku bocznym **Ustawienia osobiste** wybierz pozycję **zabezpieczenia** , a następnie wybierz pozycję **Włącz uwierzytelnianie dwuskładnikowe** w obszarze **uwierzytelnianie dwuskładnikowe** .

### <a name="add-your-github-account-to-the-app"></a>Dodawanie konta usługi GitHub do aplikacji

1. Na stronie **uwierzytelnianie dwuskładnikowe** na komputerze wybierz pozycję **Konfiguruj przy użyciu aplikacji**.

2. Zapisz kody odzyskiwania, aby móc wrócić do konta, Jeśli utracisz dostęp, a następnie wybierz przycisk **dalej**. 

    Możesz zapisać swoje kody, pobierając je na urządzenie, drukując twardą kopię lub kopiując je do narzędzia Menedżera haseł.

3. Na stronie **uwierzytelnianie dwuskładnikowe** wybierz pozycję **Konfiguruj przy użyciu aplikacji**.

    Strona zmieni się, aby wyświetlić kod QR. Nie zamykaj tej strony.

4. Otwórz aplikację Microsoft Authenticator, wybierz pozycję **Dodaj konto** z ikony **Dostosowywanie i kontrola** w prawym górnym rogu, wybierz pozycję **inne konto (Google, Facebook itp.)**, a następnie wybierz opcję **wprowadź ten kod tekstowy** z tekstu w górnej części strony.

    Aplikacja Microsoft Authenticator nie może zeskanować kodu QR, więc musisz ręcznie wprowadzić kod.

5. Wprowadź **nazwę konta** (na przykład GitHub) i wpisz **klucz tajny** w kroku 4, a następnie wybierz pozycję **Zakończ**.

6. Na stronie **wystawca uwierzytelnienia** na komputerze wpisz sześciocyfrowy kod weryfikacyjny podany w aplikacji dla konta usługi GitHub, a następnie wybierz pozycję **Włącz**.

    Na stronie **konta** aplikacji jest wyświetlana nazwa konta i sześciocyfrowy kod weryfikacyjny. W celu zapewnienia dodatkowych zabezpieczeń kod weryfikacyjny zmienia się co 30 sekund, uniemożliwiając komuś wielokrotne użycie kodu.

    >[!NOTE]
    >Aby uzyskać więcej informacji na temat weryfikacji dwuskładnikowej i konta usługi GitHub, zobacz [Informacje o uwierzytelnianiu dwuskładnikowym](https://help.github.com/articles/about-two-factor-authentication/).

## <a name="add-your-amazon-account"></a>Dodawanie konta Amazon

Dodaj konto Amazon, włączając weryfikację dwuskładnikową, a następnie dodając konto do aplikacji.

### <a name="turn-on-two-factor-verification"></a>Włącz weryfikację dwuskładnikową

1. Na komputerze otwórz pozycję Amazon, zaznacz menu rozwijane **konto & listy** , a następnie wybierz **konto**.

2. Wybierz pozycję **zaloguj & zabezpieczenia**, zaloguj się do konta Amazon, a następnie wybierz pozycję **Edytuj** w obszarze **Zaawansowane ustawienia zabezpieczeń** .

    Zostanie wyświetlona strona **Zaawansowane ustawienia zabezpieczeń** .

3. Wybierz pozycję **Rozpocznij**.

4. Wybierz pozycję **aplikacja uwierzytelniania** na stronie **Wybierz sposób otrzymywania kodów** .

    Strona zmieni się, aby wyświetlić kod QR. Nie zamykaj tej strony.

5. Otwórz aplikację Microsoft Authenticator, wybierz pozycję **Dodaj konto** z ikony **Dostosowywanie i kontrola** w prawym górnym rogu, a następnie wybierz pozycję **inne konto (Google, Facebook itp.)**.

6. Za pomocą aparatu fotograficznego urządzenia Zeskanuj kod QR na stronie **Wybierz sposób otrzymywania kodów** na komputerze.

    >[!Note]
    >Jeśli aparat nie działa prawidłowo, można ręcznie wprowadzić kod QR i adres URL.

7. Przejrzyj stronę **kont** aplikacji Microsoft Authenticator na urządzeniu, aby upewnić się, że informacje o koncie są prawidłowe i że istnieje skojarzony sześciocyfrowy kod weryfikacyjny.

    W celu zapewnienia dodatkowych zabezpieczeń kod weryfikacyjny zmienia się co 30 sekund, uniemożliwiając komuś wielokrotne użycie kodu.

8. Na stronie **Wybierz sposób otrzymywania kodów** na komputerze wpisz sześciocyfrowy kod weryfikacyjny podany w aplikacji dla konta usługi Amazon, a następnie wybierz pozycję **Weryfikuj kod i Kontynuuj**.

9. Ukończ resztę procesu rejestracji, łącznie z dodaniem metody weryfikacji kopii zapasowej, takiej jak wiadomość SMS, a następnie wybierz pozycję **Wyślij kod**.

10. Na stronie **Dodaj metodę weryfikacji kopii zapasowej** na komputerze wpisz sześciocyfrowy kod weryfikacyjny dostarczony przez metodę weryfikacji kopii zapasowej dla konta Amazon, a następnie wybierz pozycję **Weryfikuj kod i Kontynuuj**.

11. Na **prawie gotowe** strony Zdecyduj, czy komputer ma być zaufanym urządzeniem, a następnie wybierz **go. Włącz weryfikację Two-Step**.

    Zostanie wyświetlona strona **Zaawansowane ustawienia zabezpieczeń** zawierająca zaktualizowane dane weryfikacji dwuskładnikowej.

    >[!NOTE]
    >Aby uzyskać więcej informacji na temat weryfikacji dwuskładnikowej i konta Amazon, zobacz [Informacje o weryfikacji Two-Step](https://www.amazon.com/gp/help/customer/display.html?nodeId=201596330) i [zalogowaniu się przy użyciu weryfikacji Two-Step](https://www.amazon.com/gp/help/customer/display.html?nodeId=201962440).

## <a name="next-steps"></a>Następne kroki

- Po dodaniu kont do aplikacji możesz zalogować się przy użyciu aplikacji uwierzytelniania na urządzeniu. Aby uzyskać więcej informacji, zobacz [Logowanie przy użyciu aplikacji](user-help-auth-app-sign-in.md).

- W przypadku urządzeń z systemem iOS można również utworzyć kopię zapasową poświadczeń konta i powiązanych ustawień aplikacji, takich jak kolejność kont, w chmurze. Aby uzyskać więcej informacji, zobacz [Tworzenie kopii zapasowych i odzyskiwanie danych za pomocą aplikacji Microsoft Authenticator](user-help-auth-app-backup-recovery.md).
