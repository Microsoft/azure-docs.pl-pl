---
title: Konfigurowanie aplikacji uwierzytelniania jako metody weryfikacji dwuskładnikowej — Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak skonfigurować aplikację Microsoft Authenticator jako metodę weryfikacji dwuskładnikowej.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 08/12/2019
ms.author: curtand
ms.openlocfilehash: 10b6b788077eccda522c9e8c6e631939ca1819fc
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91530344"
---
# <a name="set-up-an-authenticator-app-as-your-two-factor-verification-method"></a>Konfigurowanie aplikacji uwierzytelniania jako metody weryfikacji dwuskładnikowej

Można skonfigurować aplikację uwierzytelniającej do wysyłania powiadomienia do urządzenia przenośnego lub do wysyłania kodu weryfikacyjnego jako metody weryfikacji zabezpieczeń. Nie jest wymagane korzystanie z aplikacji Microsoft Authenticator i można wybrać inną aplikację podczas procesu konfigurowania. Jednak w tym artykule jest stosowana aplikacja Microsoft Authenticator.

>[!Important]
>Aby można było dodać konto, należy pobrać i zainstalować aplikację Microsoft Authenticator. Jeśli jeszcze tego nie zrobiono, wykonaj kroki opisane w artykule [pobieranie i instalowanie aplikacji](user-help-auth-app-download-install.md) .

>[!Note]
> Jeśli opcja aplikacji mobilnej jest wyszarzona, istnieje możliwość, że Twoja organizacja nie zezwala na używanie aplikacji uwierzytelniania do weryfikacji. W takim przypadku należy wybrać inną metodę lub skontaktować się z administratorem w celu uzyskania dodatkowej pomocy.

## <a name="set-up-the-microsoft-authenticator-app-to-send-notifications"></a>Konfigurowanie aplikacji Microsoft Authenticator do wysyłania powiadomień

1. Na [stronie dodatkowa Weryfikacja zabezpieczeń](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1)wybierz pozycję **aplikacja mobilna** z **kroku 1: jak należy się skontaktować** z Twoim obszarem.

2. Wybierz pozycję **Otrzymuj powiadomienia, aby przeprowadzić weryfikację** , w obszarze **jak chcesz korzystać z aplikacji mobilnej** , a następnie wybierz pozycję **Konfiguruj**.

    ![Zrzut ekranu przedstawiający stronę "Dodatkowa weryfikacja zabezpieczeń" z wybraną opcją "aplikacja mobilna" i "otrzymywanie powiadomień o weryfikacji".](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-notification.png)

    Zostanie wyświetlona strona **Konfigurowanie aplikacji mobilnej** .

    ![Ekran, który zawiera kod QR](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-barcode.png)

3. Otwórz aplikację Microsoft Authenticator, wybierz pozycję **Dodaj konto** na stronie ikona **Dostosowywanie i kontrola** w prawym górnym rogu, a następnie wybierz pozycję **konto służbowe**.

    >[!Note]
    >Jeśli konfigurujesz aplikację Microsoft Authenticator po raz pierwszy, może zostać wyświetlony monit z pytaniem, czy zezwolić aplikacji na dostęp do aparatu (iOS), czy zezwolić aplikacji na wykonywanie zdjęć i nagrywanie wideo (Android). Musisz wybrać opcję **Zezwól** , aby aplikacja Authenticator mogła uzyskać dostęp do Twojego aparatu, aby w następnym kroku uzyskać zdjęcie kodu QR. Jeśli nie zezwolisz na korzystanie z aparatu, nadal możesz skonfigurować aplikację uwierzytelniającej, ale musisz ręcznie dodać informacje o kodzie. Aby dowiedzieć się, jak ręcznie dodać kod, zobacz temat [Ręczne dodawanie konta do aplikacji](user-help-auth-app-add-account-manual.md).

4. Za pomocą aparatu urządzenia Przeskanuj kod QR na ekranie **Konfigurowanie aplikacji mobilnej** na komputerze, a następnie wybierz przycisk **dalej**.

5. Wróć do komputera i **dodatkowej weryfikacji zabezpieczeń** , upewnij się, że otrzymujesz komunikat informujący, że konfiguracja zakończyła się pomyślnie, a następnie wybierz przycisk **dalej**.

    ![Zrzut ekranu przedstawiający stronę "Dodatkowa weryfikacja zabezpieczeń" z konfiguracją "aplikacja mobilna". wyróżniono komunikat o powodzeniu.](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-notification-confirm.png)

    Aplikacja Authenticator wyśle powiadomienie do urządzenia przenośnego w celu przetestowania.

6. Na urządzeniu przenośnym wybierz pozycję **Zatwierdź**.

7. Na komputerze Dodaj numer telefonu urządzenia przenośnego do **kroku 3: w przypadku utraty dostępu do obszaru aplikacji mobilnej** , a następnie wybierz przycisk **dalej**.

    Zdecydowanie zalecamy dodanie numeru telefonu urządzenia przenośnego, aby działał jako kopia zapasowa, jeśli nie możesz uzyskać do niej dostępu lub użyć aplikacji mobilnej z dowolnego powodu.

8. W **kroku 4: nadal korzystać z istniejących aplikacji** Skopiuj podane hasło aplikacji i wklej je w bezpiecznym miejscu.

    ![Obszar haseł aplikacji na stronie dodatkowej weryfikacji zabezpieczeń](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Aby uzyskać informacje o sposobach używania hasła aplikacji ze starszymi aplikacjami, zobacz [Zarządzanie hasłami aplikacji](multi-factor-authentication-end-user-app-passwords.md). Hasła aplikacji należy używać tylko w przypadku kontynuowania korzystania ze starszych aplikacji, które nie obsługują weryfikacji dwuskładnikowej.

9. Wybierz pozycję **Gotowe**.

## <a name="set-up-the-microsoft-authenticator-app-to-use-verification-codes"></a>Konfigurowanie aplikacji Microsoft Authenticator do używania kodów weryfikacyjnych

1. Na stronie **dodatkowa Weryfikacja zabezpieczeń** wybierz pozycję **aplikacja mobilna** z **kroku 1: jak należy się skontaktować** z Twoim obszarem.

2. Wybierz pozycję **Użyj kodu weryfikacyjnego** z obszaru **jak chcesz korzystać z aplikacji mobilnej** , a następnie wybierz pozycję **Konfiguruj**.

    ![Strona dodatkowej weryfikacji zabezpieczeń z opcją aplikacji mobilnych i powiadomień](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-verification-code.png)

    Zostanie wyświetlona strona **Konfigurowanie aplikacji mobilnej** .

    ![Ekran, który zawiera kod QR](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-barcode.png)

3. Otwórz aplikację Microsoft Authenticator, wybierz pozycję **Dodaj konto** na stronie ikona **Dostosowywanie i kontrola** w prawym górnym rogu, a następnie wybierz pozycję **konto służbowe**.

    >[!Note]
    >Jeśli konfigurujesz aplikację Microsoft Authenticator po raz pierwszy, może zostać wyświetlony monit z pytaniem, czy zezwolić aplikacji na dostęp do aparatu (iOS), czy zezwolić aplikacji na wykonywanie zdjęć i nagrywanie wideo (Android). Musisz wybrać opcję **Zezwól** , aby aplikacja Authenticator mogła uzyskać dostęp do Twojego aparatu, aby w następnym kroku uzyskać zdjęcie kodu QR. Jeśli nie zezwolisz na korzystanie z aparatu, nadal możesz skonfigurować aplikację uwierzytelniającej, ale musisz ręcznie dodać informacje o kodzie. Aby dowiedzieć się, jak ręcznie dodać kod, zobacz temat [Ręczne dodawanie konta do aplikacji](user-help-auth-app-add-account-manual.md).

4. Za pomocą aparatu urządzenia Przeskanuj kod QR na ekranie **Konfigurowanie aplikacji mobilnej** na komputerze, a następnie wybierz przycisk **dalej**.

5. Wróć do komputera i **dodatkowej weryfikacji zabezpieczeń** , upewnij się, że otrzymujesz komunikat informujący, że konfiguracja zakończyła się pomyślnie, a następnie wybierz przycisk **dalej**.

    ![Dodatkowa strona weryfikacji zabezpieczeń z komunikatem o powodzeniu](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-verification-confirm.png)

    Aplikacja Authenticator wyśle zapytanie o kod weryfikacyjny jako test.

6. W aplikacji Microsoft Authenticator przewiń w dół do swojego konta służbowego, skopiuj i wklej 6-cyfrowy kod z aplikacji do **kroku 2: Wprowadź kod weryfikacyjny z aplikacji mobilnej** na komputerze, a następnie wybierz pozycję **Weryfikuj**.

    ![Strona dodatkowej weryfikacji zabezpieczeń z testem kodu weryfikacyjnego](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-verification-test.png)

7. Na komputerze Dodaj numer telefonu urządzenia przenośnego do **kroku 3: w przypadku utraty dostępu do obszaru aplikacji mobilnej** , a następnie wybierz przycisk **dalej**.

    Zdecydowanie zalecamy dodanie numeru telefonu urządzenia przenośnego, aby działał jako kopia zapasowa, jeśli nie możesz uzyskać do niej dostępu lub użyć aplikacji mobilnej z dowolnego powodu.

8. W **kroku 4: nadal korzystać z istniejących aplikacji** Skopiuj podane hasło aplikacji i wklej je w bezpiecznym miejscu.

    ![Obszar haseł aplikacji na stronie dodatkowej weryfikacji zabezpieczeń](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Aby uzyskać informacje o sposobach używania hasła aplikacji ze starszymi aplikacjami, zobacz [Zarządzanie hasłami aplikacji](multi-factor-authentication-end-user-app-passwords.md). Hasła aplikacji należy używać tylko w przypadku kontynuowania korzystania ze starszych aplikacji, które nie obsługują weryfikacji dwuskładnikowej.

9. Wybierz pozycję **Gotowe**.

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu metody weryfikacji dwuskładnikowej możesz dodać kolejne metody, zarządzać ustawieniami i hasłami aplikacji, zalogować się lub uzyskać pomoc dotyczącą niektórych typowych problemów związanych z weryfikacją dwuskładnikowym.

- [Zarządzanie ustawieniami metody weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-manage-settings.md)

- [Zarządzanie hasłami aplikacji](multi-factor-authentication-end-user-app-passwords.md)

- [Logowanie przy użyciu weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-signin.md)

- [Uzyskaj pomoc dotyczącą weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-troubleshoot.md)
