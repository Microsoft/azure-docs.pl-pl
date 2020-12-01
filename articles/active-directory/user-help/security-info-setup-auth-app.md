---
title: Konfigurowanie aplikacji Microsoft Authenticator jako metody weryfikacji — Azure AD
description: Jak skonfigurować stronę informacje zabezpieczające (wersja zapoznawcza) w celu zweryfikowania tożsamości przy użyciu aplikacji Microsoft Authenticator jako metody weryfikacji.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 02/13/2019
ms.author: curtand
ms.openlocfilehash: e383896e50ccf06ef6d1b5caaeb0ef36ece2ce3b
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348300"
---
# <a name="set-up-the-microsoft-authenticator-app-as-your-verification-method"></a>Konfigurowanie aplikacji Microsoft Authenticator jako metody weryfikacji

Aby dodać weryfikację dwuskładnikową i metodę resetowania hasła, można wykonać następujące czynności. Po wybraniu tej opcji po raz pierwszy możesz wrócić do strony **informacje zabezpieczające** , aby dodać, zaktualizować lub usunąć informacje o zabezpieczeniach.

Jeśli zostanie wyświetlony monit o ustawienie tej opcji natychmiast po zalogowaniu się do konta służbowego, Zobacz szczegółowe kroki opisane w artykule [Konfigurowanie informacji zabezpieczających na stronie Logowanie](security-info-setup-signin.md) .

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
> Jeśli nie widzisz opcji aplikacji Authenticator, istnieje możliwość, że Twoja organizacja nie zezwala na korzystanie z tej opcji w celu weryfikacji. W takim przypadku należy wybrać inną metodę lub skontaktować się z działem pomocy technicznej w organizacji, aby uzyskać pomoc.

## <a name="security-vs-password-reset-verification"></a>Weryfikacja resetowania hasła do zabezpieczeń

Metody informacji zabezpieczających są używane do weryfikacji zabezpieczeń dwuskładnikowych i resetowania hasła. Jednak nie wszystkie metody mogą służyć do obu celów.

| Metoda | Sposób użycia |
| ------ | -------- |
| Aplikacja Authenticator | Weryfikacja dwuskładnikowa i uwierzytelnianie za pomocą resetowania hasła. |
| Wiadomości SMS | Weryfikacja dwuskładnikowa i uwierzytelnianie za pomocą resetowania hasła. |
| Rozmowy telefoniczne | Weryfikacja dwuskładnikowa i uwierzytelnianie za pomocą resetowania hasła. |
| Klucz zabezpieczeń | Weryfikacja dwuskładnikowa i uwierzytelnianie za pomocą resetowania hasła. |
| Konto e-mail | Tylko uwierzytelnianie za pomocą resetowania hasła. Należy wybrać inną metodę na potrzeby weryfikacji dwuskładnikowej. |
| Pytania zabezpieczające | Tylko uwierzytelnianie za pomocą resetowania hasła. Należy wybrać inną metodę na potrzeby weryfikacji dwuskładnikowej. |

## <a name="set-up-the-microsoft-authenticator-app-from-the-security-info-page"></a>Konfigurowanie aplikacji Microsoft Authenticator na stronie z informacjami o zabezpieczeniach

W zależności od ustawień organizacji może być możliwe użycie aplikacji uwierzytelniania jako jednej z metod informacji zabezpieczających. Nie jest wymagane korzystanie z aplikacji Microsoft Authenticator i można wybrać inną aplikację podczas procesu konfiguracji. Jednak w tym artykule jest stosowana aplikacja Microsoft Authenticator.

>[!Important]
> Jeśli masz Instalatora Microsoft Authenticator aplikacji na 5 różnych urządzeniach lub 5 tokenów sprzętowych, nie będzie można skonfigurować szóstej i zobaczyć następujący komunikat o błędzie.
> 
> **Nie można skonfigurować Microsoft Authenticator, ponieważ masz już pięć aplikacji lub tokenów sprzętowych uwierzytelniania. Skontaktuj się z administratorem, aby usunąć jedną z aplikacji uwierzytelniania lub tokenów sprzętowych.**

### <a name="to-set-up-the-microsoft-authenticator-app"></a>Aby skonfigurować aplikację Microsoft Authenticator

1. Zaloguj się do swojego konta służbowego, a następnie przejdź do https://myaccount.microsoft.com/ strony.

    ![Strona mój profil zawierająca wyróżnione linki do informacji zabezpieczających](media/security-info/securityinfo-myprofile-page.png)

2. W okienku nawigacji po lewej stronie wybierz pozycję **informacje zabezpieczające** lub link w bloku **informacje zabezpieczające** , a następnie wybierz pozycję **Dodaj metodę** ze strony **informacje zabezpieczające** .

    ![Strona z informacjami o zabezpieczeniach z wyróżnioną opcją dodawania metody](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na stronie **Dodawanie metody** wybierz z listy rozwijanej pozycję **aplikacja uwierzytelniania** , a następnie wybierz pozycję **Dodaj**.

    ![Pole dodawania metody z wybraną aplikacją Authenticator](media/security-info/securityinfo-myprofile-addauthapp.png)

4. Na stronie **Rozpocznij od pobrania aplikacji** wybierz pozycję **Pobierz teraz** , aby pobrać i zainstalować aplikację Microsoft Authenticator na urządzeniu przenośnym, a następnie wybierz przycisk **dalej**.

    Aby uzyskać więcej informacji o pobieraniu i instalowaniu aplikacji, zobacz [Pobieranie i instalowanie aplikacji Microsoft Authenticator](user-help-auth-app-download-install.md).

    ![Zacznij od pobrania strony aplikacji](media/security-info/securityinfo-myprofile-getauthapp.png)

   > [!Note]
   > Jeśli chcesz użyć aplikacji uwierzytelniającej innej niż aplikacja Microsoft Authenticator, wybierz link **Chcę użyć innej aplikacji uwierzytelniania**.
   >
   > Jeśli organizacja umożliwia wybranie innej metody niż aplikacja uwierzytelniająca, możesz wybrać **link Chcę skonfigurować inną metodę**.

5. Pozostań na stronie **Konfigurowanie konta** podczas konfigurowania aplikacji Microsoft Authenticator na urządzeniu mobilnym.

    ![Konfigurowanie strony aplikacji uwierzytelniania](media/security-info/securityinfo-myprofile-setupauthapp.png)

6. Otwórz aplikację Microsoft Authenticator, zezwól na powiadomienia (jeśli pojawi się monit), wybierz pozycję **Dodaj konto** z menu ikony **Dostosowywanie i kontrolowanie** w prawym górnym rogu, a następnie wybierz pozycję **Konto służbowe**.

    >[!Note]
    >Jeśli konfigurujesz aplikację Microsoft Authenticator po raz pierwszy, może zostać wyświetlony monit z pytaniem, czy zezwolić aplikacji na dostęp do aparatu (iOS), czy zezwolić aplikacji na wykonywanie zdjęć i nagrywanie wideo (Android). Musisz wybrać opcję **Zezwól** , aby aplikacja Authenticator mogła uzyskać dostęp do Twojego aparatu, aby w następnym kroku uzyskać zdjęcie kodu QR. Jeśli nie zezwolisz na korzystanie z aparatu, nadal możesz skonfigurować aplikację uwierzytelniającej, ale musisz ręcznie dodać informacje o kodzie. Aby dowiedzieć się, jak ręcznie dodać kod, zobacz temat [Ręczne dodawanie konta do aplikacji](user-help-auth-app-add-account-manual.md).

7. Wróć na stronę **Konfigurowanie konta** na komputerze, a następnie wybierz pozycję **Dalej**.

    Zostanie wyświetlona strona **Skanowanie kodu QR**.

    ![Skanowanie kodu QR przy użyciu aplikacji Authenticator](media/security-info/securityinfo-myprofile-qrcodeauthapp.png)

8. Zeskanuj udostępniony kod za pomocą czytnika kodu QR aplikacji Microsoft Authenticator, który pojawił się na urządzeniu przenośnym po utworzeniu konta służbowego w kroku 6.

    Aplikacja Authenticator powinna pomyślnie dodać konto służbowe bez konieczności podawania żadnych dodatkowych informacji przez użytkownika. Jednak jeśli czytnik kodów QR nie może odczytać kodu, możesz wybrać **link Nie mogę zeskanować kodu QR** oraz ręcznie podać kod i adres URL w aplikacji Microsoft Authenticator. Aby uzyskać więcej informacji na temat ręcznego dodawania kodu, zobacz [Ręcznie dodawanie konta do aplikacji](user-help-auth-app-add-account-manual.md).

9. Wybierz pozycję **Dalej** na stronie **Zeskanuj kod QR** na komputerze.

    Do aplikacji Microsoft Authenticator na urządzeniu przenośnym zostanie wysłane powiadomienie o konieczności przetestowania konta.

    ![Testowanie konta za pomocą aplikacji Authenticator](media/security-info/securityinfo-myprofile-tryitauthapp.png)

10. Zatwierdź powiadomienie w aplikacji Microsoft Authenticator, a następnie wybierz pozycję **Dalej**.

     ![Powiadomienie o powodzeniu łączenia aplikacji z kontem](media/security-info/securityinfo-myprofile-successauthapp.png)

     Informacje zabezpieczające zostały zaktualizowane, tak aby aplikacja Microsoft Authenticator była domyślnie używana do weryfikowania tożsamości podczas korzystania z weryfikacji dwuetapowej lub resetowania hasła.

## <a name="delete-your-authenticator-app-from-your-security-info-methods"></a>Usuwanie aplikacji uwierzytelniania z metod informacji zabezpieczających

Jeśli nie chcesz już używać aplikacji uwierzytelniania jako metody informacji zabezpieczających, możesz usunąć ją ze strony z **informacjami o zabezpieczeniach** . Działa to w przypadku wszystkich aplikacji uwierzytelniania, a nie tylko aplikacji Microsoft Authenticator. Po usunięciu aplikacji należy przejść do aplikacji uwierzytelniania na urządzeniu przenośnym i usunąć konto.

>[!Important]
>Jeśli usuniesz aplikację Authenticator przez pomyłkę, nie ma możliwości jej cofnięcia. Musisz ponownie dodać aplikację uwierzytelniającej, wykonując czynności opisane w sekcji [Konfigurowanie aplikacji uwierzytelniania](#set-up-the-microsoft-authenticator-app-from-the-security-info-page) w tym artykule.

### <a name="to-delete-the-authenticator-app"></a>Aby usunąć aplikację Authenticator

1. Na stronie **Informacje o zabezpieczeniach** wybierz łącze **Usuń** obok aplikacji wystawca.

    ![Link umożliwiający usunięcie aplikacji Authenticator z informacji zabezpieczających](media/security-info/securityinfo-myprofile-deleteauthapp.png)

2. Wybierz opcję **tak** w polu potwierdzenia, aby usunąć aplikację Authenticator. Po usunięciu aplikacji uwierzytelniającej jest ona usuwana z informacji zabezpieczających i znika ze strony z **informacjami o zabezpieczeniach** . Jeśli aplikacja Authenticator jest metodą domyślną, zmieni się na inną dostępną metodę.

3. Otwórz aplikację Authenticator na swoim urządzeniu przenośnym, wybierz pozycję **Edytuj konta**, a następnie usuń konto służbowe z aplikacji Authenticator.

    Twoje konto zostało całkowicie usunięte z aplikacji Authenticator na potrzeby weryfikacji dwuskładnikowej i żądań resetowania haseł.

## <a name="change-your-default-security-info-method"></a>Zmień domyślną metodę informacji zabezpieczających

Jeśli chcesz, aby aplikacja Authenticator była metodą domyślną używaną podczas logowania do konta służbowego przy użyciu weryfikacji dwuskładnikowej lub żądania resetowania hasła, możesz ustawić ją na stronie **informacje** zabezpieczające.

### <a name="to-change-your-default-security-info-method"></a>Aby zmienić domyślną metodę informacji zabezpieczających

1. Na stronie **Informacje o zabezpieczeniach** wybierz łącze **Zmień** obok domyślnych informacji o **metodzie logowania** .

    ![Zmień link dla domyślnej metody logowania](media/security-info/securityinfo-myprofile-changedefaultauthapp.png)

2. Wybierz pozycję **Microsoft Authenticator — powiadomienie** z listy rozwijanej dostępnych metod. Jeśli nie używasz aplikacji Microsoft Authenticator, wybierz opcję **aplikacja Authenticator lub token sprzętowy** .

    ![Wybierz metodę logowania domyślnego](media/security-info/securityinfo-myprofile-defaultauthapp.png)

3. Wybierz pozycję **Potwierdź**.

    Domyślna Metoda służąca do wprowadzania zmian w aplikacji Microsoft Authenticator.

## <a name="additional-security-info-methods"></a>Dodatkowe metody informacji zabezpieczających

Dostępne są dodatkowe opcje dotyczące sposobu, w jaki Twoja organizacja kontaktuje się z Twoją tożsamością, w oparciu o to, co you're próbę wykonania. Dostępne są następujące opcje:

- **Tekst urządzenia przenośnego.** Wprowadź numer urządzenia przenośnego i uzyskaj tekst kodu, który będzie używany do weryfikacji dwuetapowej lub resetowania hasła. Aby uzyskać instrukcje krok po kroku dotyczące weryfikowania tożsamości za pomocą wiadomości tekstowych (SMS), zobacz [Konfigurowanie informacji zabezpieczających do używania wiadomości](security-info-setup-text-msg.md)SMS.

- **Urządzenie przenośne lub połączenie telefoniczne.** Wprowadź numer urządzenia przenośnego i otrzymaj połączenie telefoniczne w celu weryfikacji dwuetapowej lub resetowania hasła. Aby uzyskać instrukcje krok po kroku dotyczące weryfikowania tożsamości przy użyciu numeru telefonu, zobacz [Konfigurowanie informacji zabezpieczających do korzystania z połączeń telefonicznych](security-info-setup-phone-number.md).

- **Klucz zabezpieczeń.** Zarejestruj klucz zabezpieczeń zgodny z firmą Microsoft i użyj go wraz z numerem PIN w celu weryfikacji dwuetapowej lub resetowania hasła. Instrukcje krok po kroku dotyczące weryfikowania tożsamości przy użyciu klucza zabezpieczeń znajdują się w temacie [set up Security Info to use a Security Key](security-info-setup-security-key.md).

- **Adres e-mail.** Wprowadź służbowy adres e-mail, aby uzyskać wiadomość e-mail na potrzeby resetowania hasła. Ta opcja jest niedostępna w przypadku weryfikacji dwuetapowej. Aby uzyskać instrukcje krok po kroku dotyczące konfigurowania poczty e-mail, zobacz [Konfigurowanie informacji zabezpieczających do używania poczty e-mail](security-info-setup-email.md).

- **Pytania zabezpieczające.** Odpowiedz na niektóre pytania zabezpieczające utworzone przez administratora w organizacji. Ta opcja jest dostępna tylko do resetowania haseł, a nie do weryfikacji dwuetapowej. Aby uzyskać instrukcje krok po kroku dotyczące konfigurowania pytań zabezpieczających, zobacz artykuł [Konfigurowanie informacji zabezpieczających do używania pytań zabezpieczających](security-info-setup-questions.md) .

    >[!Note]
    >Jeśli brakuje niektórych z tych opcji, prawdopodobnie organizacja nie zezwala na te metody. W takim przypadku należy wybrać dostępną metodę lub skontaktować się z administratorem w celu uzyskania pomocy.

## <a name="next-steps"></a>Następne kroki

- Zaloguj się przy użyciu aplikacji Microsoft Authenticator, wykonując czynności opisane w artykule [Logowanie przy użyciu weryfikacji dwuetapowej lub informacji o zabezpieczeniach](security-info-setup-signin.md) .

- Zagubione lub zapomniane hasło możesz zresetować w [portalu resetowania hasła](https://passwordreset.microsoftonline.com/) lub wykonując kroki przedstawione w artykule [Resetowanie hasła służbowego](active-directory-passwords-update-your-own-password.md).

- Wskazówki i pomoc dotyczące rozwiązywania problemów z logowaniem znajdują się w artykule [Nie można zalogować się na konto Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
