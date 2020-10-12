---
title: Konfigurowanie numeru telefonu jako metody weryfikacji — Azure AD
description: Jak skonfigurować informacje zabezpieczające (wersja zapoznawcza) w celu zweryfikowania tożsamości przy użyciu numeru telefonu i urządzenia przenośnego jako metody weryfikacji.
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
ms.openlocfilehash: 7500b6b146a627dcdebacd9d2e2c7c61bc43c105
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "83744444"
---
# <a name="set-up-a-phone-number-as-your-verification-method"></a>Konfigurowanie numeru telefonu jako metody weryfikacji

Aby dodać weryfikację dwuskładnikową i metodę resetowania hasła, można wykonać następujące czynności. Po wybraniu tej opcji po raz pierwszy możesz wrócić do strony **informacje zabezpieczające** , aby dodać, zaktualizować lub usunąć informacje o zabezpieczeniach.

Jeśli zostanie wyświetlony monit o ustawienie tej opcji natychmiast po zalogowaniu się do konta służbowego, Zobacz szczegółowe kroki opisane w artykule [Konfigurowanie informacji zabezpieczających na stronie Logowanie](security-info-setup-signin.md) .

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

> [!Note]
> Informacje zabezpieczające nie obsługują używania rozszerzeń telefonu. Nawet jeśli dodasz właściwy format, + 1 4255551234X12345, rozszerzenia zostaną usunięte przed umieszczeniem wywołania.
>
> Jeśli nie widzisz opcji telefon, być może Twoja organizacja nie zezwala na korzystanie z tej opcji w celu weryfikacji. W takim przypadku należy wybrać inną metodę lub skontaktować się z działem pomocy technicznej w organizacji, aby uzyskać pomoc.

## <a name="security-verification-versus-password-reset-authentication"></a>Weryfikacja zabezpieczeń a uwierzytelnianie resetowania hasła

Metody informacji zabezpieczających są używane do weryfikacji zabezpieczeń dwuskładnikowych i resetowania hasła. Jednak nie wszystkie metody mogą służyć do obu celów.

| Metoda | Sposób użycia |
| ------ | -------- |
| Aplikacja Authenticator | Weryfikacja dwuskładnikowa i uwierzytelnianie za pomocą resetowania hasła. |
| Wiadomości SMS | Weryfikacja dwuskładnikowa i uwierzytelnianie za pomocą resetowania hasła. |
| Rozmowy telefoniczne | Weryfikacja dwuskładnikowa i uwierzytelnianie za pomocą resetowania hasła. |
| Klucz zabezpieczeń | Weryfikacja dwuskładnikowa i uwierzytelnianie za pomocą resetowania hasła. |
| Konto e-mail | Tylko uwierzytelnianie za pomocą resetowania hasła. Należy wybrać inną metodę na potrzeby weryfikacji dwuskładnikowej. |
| Pytania zabezpieczające | Tylko uwierzytelnianie za pomocą resetowania hasła. Należy wybrać inną metodę na potrzeby weryfikacji dwuskładnikowej. |

## <a name="set-up-phone-calls-from-the-security-info-page"></a>Konfigurowanie połączeń telefonicznych ze strony z informacjami o zabezpieczeniach

W zależności od ustawień organizacji może być możliwe użycie połączeń telefonicznych jako jednej z metod informacji zabezpieczających.

>[!Note]
>Jeśli chcesz otrzymać wiadomość tekstową zamiast połączenia telefonicznego, postępuj zgodnie z instrukcjami w artykule [Konfigurowanie informacji zabezpieczających do korzystania z wiadomości tekstowych](security-info-setup-text-msg.md) .

### <a name="to-set-up-phone-calls"></a>Aby skonfigurować połączenia telefoniczne

1. Zaloguj się do swojego konta służbowego, a następnie przejdź do https://myaccount.microsoft.com/ strony.

    ![Strona mój profil zawierająca wyróżnione linki do informacji zabezpieczających](media/security-info/securityinfo-myprofile-page.png)

2. W okienku nawigacji po lewej stronie wybierz pozycję **informacje zabezpieczające** lub link w bloku **informacje zabezpieczające** , a następnie wybierz pozycję **Dodaj metodę** ze strony **informacje zabezpieczające** .

    ![Strona z informacjami o zabezpieczeniach z wyróżnioną opcją dodawania metody](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na stronie **Dodawanie metody** wybierz pozycję **telefon** z listy rozwijanej, a następnie wybierz pozycję **Dodaj**.

    ![Dodaj pole metody z wybranym numerem telefonu](media/security-info/securityinfo-myprofile-addphonetext.png)

4. Na stronie **telefon** wpisz numer telefonu urządzenia przenośnego, wybierz opcję **Zadzwoń do mnie**, a następnie wybierz przycisk **dalej**.

    ![Dodawanie numeru telefonu i Wybieranie połączeń telefonicznych](media/security-info/securityinfo-myprofile-phonecall-addnumber.png)

5. Odpowiedz na połączenie telefoniczne weryfikacyjne, wysłane na numer telefonu, a następnie postępuj zgodnie z instrukcjami.

    Strona zmieni się, aby pokazać sukces.

    ![Powiadomienie o powodzeniu, połączenie numeru telefonu, wybór, aby odbierać połączenia telefoniczne i Twoje konto](media/security-info/securityinfo-myprofile-phonetext-success.png)

    Informacje zabezpieczające są aktualizowane i można użyć połączeń telefonicznych, aby zweryfikować swoją tożsamość podczas korzystania z weryfikacji dwuetapowej lub resetowania hasła. Jeśli chcesz nawiązać połączenie telefoniczne z domyślną metodą, zobacz sekcję [Zmienianie domyślnej metody informacyjnej zabezpieczeń](#change-your-default-security-info-method) w tym artykule.

## <a name="delete-phone-calls-from-your-security-info-methods"></a>Usuwanie połączeń telefonicznych z metod informacji zabezpieczających

Jeśli nie chcesz już używać połączeń telefonicznych jako metody informacji zabezpieczających, możesz usunąć ją ze strony z **informacjami o zabezpieczeniach** .

>[!Important]
>W przypadku usuwania połączeń telefonicznych przez pomyłkę nie można jej cofnąć. Musisz ponownie dodać metodę, wykonując czynności opisane w sekcji [Konfigurowanie połączeń telefonicznych](#set-up-phone-calls-from-the-security-info-page) w tym artykule.

### <a name="to-delete-phone-calls"></a>Aby usunąć połączenia telefoniczne

1. Na stronie **informacje zabezpieczające** wybierz łącze **Usuń** obok opcji **telefon** .

    ![Link umożliwiający usunięcie metody telefonu z informacji zabezpieczających](media/security-info/securityinfo-myprofile-phonetext-delete.png)

2. Wybierz opcję **tak** w polu potwierdzenia, aby usunąć numer **telefonu** . Po usunięciu numeru telefonu zostanie on usunięty z informacji zabezpieczających i zniknie ze strony z **informacjami o zabezpieczeniach** . Jeśli **telefon** jest metodą domyślną, domyślnie zmieni się na inną dostępną metodę.

## <a name="change-your-default-security-info-method"></a>Zmień domyślną metodę informacji zabezpieczających

Jeśli chcesz, aby wywołania telefoniczne były metodą domyślną używaną podczas logowania do konta służbowego przy użyciu weryfikacji dwuskładnikowej lub żądania resetowania hasła, możesz ustawić ją na stronie **informacje zabezpieczające** .

### <a name="to-change-your-default-security-info-method"></a>Aby zmienić domyślną metodę informacji zabezpieczających

1. Na stronie **Informacje o zabezpieczeniach** wybierz łącze **Zmień** obok domyślnych informacji o **metodzie logowania** .

    ![Zmień link dla domyślnej metody logowania](media/security-info/securityinfo-myprofile-phonetext-defaultchange.png)

2. Wybierz pozycję **telefon-połączenie (*_your_phone_number_*)** z listy rozwijanej dostępne metody, a następnie wybierz pozycję **Potwierdź**.

    ![Wybierz metodę logowania domyślnego](media/security-info/securityinfo-myprofile-phonecall-changeddefault.png)

    Domyślna metoda używana do wprowadzania zmian na **telefonie (*_your_phone_number_*)**.

## <a name="additional-security-info-methods"></a>Dodatkowe metody informacji zabezpieczających

Dostępne są dodatkowe opcje dotyczące sposobu, w jaki Twoja organizacja kontaktuje się z Twoją tożsamością, w oparciu o to, co you're próbę wykonania. Dostępne są następujące opcje:

- **Aplikacja uwierzytelniania.** Pobierz aplikację Authenticator i użyj jej do uzyskania powiadomienia o zatwierdzeniu lub losowo wygenerowanego kodu zatwierdzenia na potrzeby weryfikacji dwuetapowej lub resetowania hasła. Instrukcje krok po kroku dotyczące konfigurowania i używania aplikacji Microsoft Authenticator można znaleźć w temacie [set up Security Info to use the Authenticator App](security-info-setup-auth-app.md).

- **Tekst urządzenia przenośnego.** Wprowadź numer urządzenia przenośnego i uzyskaj tekst kodu, który będzie używany do weryfikacji dwuetapowej lub resetowania hasła. Aby uzyskać instrukcje krok po kroku dotyczące weryfikowania tożsamości za pomocą wiadomości tekstowych (SMS), zobacz [Konfigurowanie informacji zabezpieczających do używania wiadomości](security-info-setup-text-msg.md)SMS.

- **Klucz zabezpieczeń.** Zarejestruj klucz zabezpieczeń zgodny z firmą Microsoft i użyj go wraz z numerem PIN w celu weryfikacji dwuetapowej lub resetowania hasła. Instrukcje krok po kroku dotyczące weryfikowania tożsamości przy użyciu klucza zabezpieczeń znajdują się w temacie [set up Security Info to use a Security Key](security-info-setup-security-key.md).

- **Adres e-mail.** Wprowadź służbowy adres e-mail, aby uzyskać wiadomość e-mail na potrzeby resetowania hasła. Ta opcja jest niedostępna w przypadku weryfikacji dwuetapowej. Aby uzyskać instrukcje krok po kroku dotyczące konfigurowania poczty e-mail, zobacz [Konfigurowanie informacji zabezpieczających do używania poczty e-mail](security-info-setup-email.md).

- **Pytania zabezpieczające.** Odpowiedz na niektóre pytania zabezpieczające utworzone przez administratora w organizacji. Ta opcja jest dostępna tylko do resetowania haseł, a nie do weryfikacji dwuetapowej. Aby uzyskać instrukcje krok po kroku dotyczące konfigurowania pytań zabezpieczających, zobacz artykuł [Konfigurowanie informacji zabezpieczających do używania pytań zabezpieczających](security-info-setup-questions.md) .

    >[!Note]
    >Jeśli brakuje niektórych z tych opcji, prawdopodobnie organizacja nie zezwala na te metody. W takim przypadku należy wybrać dostępną metodę lub skontaktować się z administratorem w celu uzyskania pomocy.

## <a name="next-steps"></a>Następne kroki

- Zagubione lub zapomniane hasło możesz zresetować w [portalu resetowania hasła](https://passwordreset.microsoftonline.com/) lub wykonując kroki przedstawione w artykule [Resetowanie hasła służbowego](active-directory-passwords-update-your-own-password.md).

- Wskazówki i pomoc dotyczące rozwiązywania problemów z logowaniem znajdują się w artykule [Nie można zalogować się na konto Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
