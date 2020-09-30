---
title: Skonfiguruj urządzenie przenośne jako metodę weryfikacji dwuskładnikowej — Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak skonfigurować urządzenie przenośne jako metodę weryfikacji dwuskładnikowej.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 08/12/2019
ms.author: curtand
ms.openlocfilehash: 40af9ac692a226b12f984db447db9635cba655e4
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91530259"
---
# <a name="set-up-a-mobile-device-as-your-two-factor-verification-method"></a>Konfigurowanie urządzenia przenośnego jako metody weryfikacji dwuskładnikowej

Można skonfigurować urządzenie przenośne do działania jako metoda weryfikacji dwuetapowej. Twój telefon komórkowy może odebrać wiadomość SMS z kodem weryfikacyjnym lub rozmową telefoniczną.

>[!Note]
> Jeśli opcja telefon uwierzytelniania jest wyszarzona, istnieje możliwość, że Twoja organizacja nie zezwala na użycie numeru telefonu lub wiadomości tekstowej do weryfikacji. W takim przypadku należy wybrać inną metodę lub skontaktować się z administratorem w celu uzyskania dodatkowej pomocy.

## <a name="set-up-your-mobile-device-to-use-a-text-message-as-your-verification-method"></a>Konfigurowanie urządzenia przenośnego do używania wiadomości tekstowej jako metody weryfikacji

1. Na stronie **dodatkowa Weryfikacja zabezpieczeń** wybierz pozycję **telefon uwierzytelniania** z **kroku 1. jak należy się skontaktować** z obszarem, wybierz swój kraj lub region z listy rozwijanej, a następnie wpisz numer telefonu urządzenia przenośnego.

2. W obszarze **Metoda** wybierz opcję **Wyślij do mnie kod przez wiadomość tekstową** , a następnie wybierz przycisk **dalej**.

    ![Zrzut ekranu przedstawiający stronę "Dodatkowa weryfikacja zabezpieczeń" z wybraną pozycją "telefon uwierzytelniania" i "Wyślij do mnie kod przez wiadomość tekstową".](media/multi-factor-authentication-verification-methods/multi-factor-authentication-text-message.png)

3. Wpisz kod weryfikacyjny z wiadomości tekstowej wysyłanej przez firmę Microsoft do **kroku 2: wysłaliśmy wiadomość SMS na Twój obszar telefonu** , a następnie wybierz pozycję **Weryfikuj**.

    ![Strona dodatkowej weryfikacji zabezpieczeń z telefonem uwierzytelniania i wiadomością SMS](media/multi-factor-authentication-verification-methods/multi-factor-authentication-text-message-test.png)

4. W obszarze **krok 3. nadal korzystać z istniejących aplikacji** Skopiuj podane hasło aplikacji i wklej je w bezpiecznym miejscu.

    ![Obszar haseł aplikacji na stronie dodatkowej weryfikacji zabezpieczeń](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Aby uzyskać informacje o sposobach używania hasła aplikacji ze starszymi aplikacjami, zobacz [Zarządzanie hasłami aplikacji](multi-factor-authentication-end-user-app-passwords.md). Hasła aplikacji należy używać tylko w przypadku kontynuowania korzystania ze starszych aplikacji, które nie obsługują weryfikacji dwuskładnikowej.

5. Wybierz pozycję **Gotowe**.

## <a name="set-up-your-mobile-device-to-receive-a-phone-call"></a>Konfigurowanie urządzenia przenośnego do odbierania połączenia telefonicznego

1. Na stronie **dodatkowa Weryfikacja zabezpieczeń** wybierz pozycję **telefon uwierzytelniania** z **kroku 1. jak należy się skontaktować** z obszarem, wybierz swój kraj lub region z listy rozwijanej, a następnie wpisz numer telefonu urządzenia przenośnego.

2. Wybierz opcję **Zadzwoń do mnie** w obszarze **metody** , a następnie wybierz przycisk **dalej**.

    ![Strona dodatkowej weryfikacji zabezpieczeń z numerem telefonu i połączenia telefonicznego uwierzytelniania](media/multi-factor-authentication-verification-methods/multi-factor-authentication-phone-call.png)

3. Otrzymasz od firmy Microsoft połączenie telefoniczne, z prośbą o naciśnięcie znaku krzyżyka (#) na urządzeniu przenośnym, aby zweryfikować Twoją tożsamość.

    ![Testowanie określonego numeru telefonu](media/multi-factor-authentication-verification-methods/multi-factor-authentication-phone-call-test.png)

4. W obszarze **krok 3. nadal korzystać z istniejących aplikacji** Skopiuj podane hasło aplikacji i wklej je w bezpiecznym miejscu.

    ![Obszar haseł aplikacji na stronie dodatkowej weryfikacji zabezpieczeń](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Aby uzyskać informacje o sposobach używania hasła aplikacji ze starszymi aplikacjami, zobacz [Zarządzanie hasłami aplikacji](multi-factor-authentication-end-user-app-passwords.md). Hasła aplikacji należy używać tylko w przypadku kontynuowania korzystania ze starszych aplikacji, które nie obsługują weryfikacji dwuskładnikowej.

5. Wybierz pozycję **Gotowe**.

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu metody weryfikacji dwuskładnikowej możesz dodać kolejne metody, zarządzać ustawieniami i hasłami aplikacji, zalogować się lub uzyskać pomoc dotyczącą niektórych typowych problemów związanych z weryfikacją dwuskładnikowym.

- [Zarządzanie ustawieniami metody weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-manage-settings.md)

- [Zarządzanie hasłami aplikacji](multi-factor-authentication-end-user-app-passwords.md)

- [Logowanie przy użyciu weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-signin.md)

- [Uzyskaj pomoc dotyczącą weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-troubleshoot.md)
