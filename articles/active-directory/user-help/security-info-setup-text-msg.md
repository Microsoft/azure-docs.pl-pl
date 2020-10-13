---
title: Konfigurowanie wiadomości SMS jako metody weryfikacji — Azure AD
description: Jak skonfigurować informacje zabezpieczające (wersja zapoznawcza) w celu zweryfikowania tożsamości za pomocą wiadomości tekstowych jako metody weryfikacji.
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
ms.openlocfilehash: c68e01e0eb7c926f47c99b16efa87d23a10b6711
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91537037"
---
# <a name="set-up-text-messaging-as-your-verification-method"></a>Konfigurowanie wiadomości tekstowych jako metody weryfikacji

Aby dodać weryfikację dwuskładnikową i metodę resetowania hasła, można wykonać następujące czynności. Po wybraniu tej opcji po raz pierwszy możesz wrócić do strony **informacje zabezpieczające** , aby dodać, zaktualizować lub usunąć informacje o zabezpieczeniach.

Jeśli zostanie wyświetlony monit o ustawienie tej opcji natychmiast po zalogowaniu się do konta służbowego, Zobacz szczegółowe kroki opisane w artykule [Konfigurowanie informacji zabezpieczających na stronie Logowanie](security-info-setup-signin.md) .

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Jeśli nie widzisz opcji telefon, być może Twoja organizacja nie zezwala na korzystanie z tej opcji w celu weryfikacji. W takim przypadku należy wybrać inną metodę lub skontaktować się z działem pomocy technicznej w organizacji, aby uzyskać pomoc.

## <a name="set-up-text-messages-from-the-security-info-page"></a>Konfigurowanie wiadomości tekstowych na stronie z informacjami o zabezpieczeniach

W zależności od ustawień organizacji może być możliwe użycie wiadomości tekstowych jako jednej z metod informacji zabezpieczających. Opcja wiadomość tekstowa jest częścią opcji telefon, dlatego można ustawić wszystko tak samo jak w przypadku Twojego numeru telefonu, ale zamiast korzystać z firmy Microsoft, możesz użyć wiadomości tekstowej.

>[!Note]
>Jeśli chcesz otrzymywać połączenie telefoniczne zamiast wiadomości tekstowej, wykonaj kroki opisane w artykule [Konfigurowanie informacji zabezpieczających do korzystania z połączeń telefonicznych](security-info-setup-phone-number.md) .

### <a name="to-set-up-text-messages"></a>Aby skonfigurować wiadomości tekstowe

1. Zaloguj się do swojego konta służbowego, a następnie przejdź do https://myaccount.microsoft.com/ strony.

    ![Strona mój profil zawierająca wyróżnione linki do informacji zabezpieczających](media/security-info/securityinfo-myprofile-page.png)

2. W okienku nawigacji po lewej stronie wybierz pozycję **informacje zabezpieczające** lub link w bloku **informacje zabezpieczające** , a następnie wybierz pozycję **Dodaj metodę** ze strony **informacje zabezpieczające** .

    ![Strona z informacjami o zabezpieczeniach z wyróżnioną opcją dodawania metody](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na stronie **Dodawanie metody** wybierz pozycję **telefon** z listy rozwijanej, a następnie wybierz pozycję **Dodaj**.

    ![Dodaj pole metody z wybranym numerem telefonu](media/security-info/securityinfo-myprofile-addphonetext.png)

4. Na stronie **telefon** wpisz numer telefonu dla urządzenia przenośnego, wybierz pozycję **tekst ja kod**, a następnie wybierz przycisk **dalej**.

    ![Zrzut ekranu przedstawiający stronę "Phone" z wybraną pozycją "tekst ja kod".](media/security-info/securityinfo-myprofile-phonetext-addnumber.png)

5. Wpisz kod wysłany przez wiadomość SMS na urządzenie przenośne, a następnie wybierz przycisk **dalej**.

    ![Dodawanie numeru telefonu i wybieranie wiadomości SMS](media/security-info/securityinfo-myprofile-phonetext-entercode.png)

    Strona zmieni się, aby pokazać sukces.

    ![Powiadomienie o powodzeniu, połączenie numeru telefonu, wybór, aby odbierać wiadomości SMS i Twoje konto](media/security-info/securityinfo-myprofile-phonetext-success.png)

    Informacje zabezpieczające są aktualizowane i można użyć wiadomości tekstowych do zweryfikowania tożsamości podczas korzystania z weryfikacji dwuetapowej lub resetowania hasła. Jeśli chcesz, aby obsługa komunikatów tekstowych była metodą domyślną, zobacz sekcję [Zmienianie domyślnej metody informacyjnej zabezpieczeń](#change-your-default-security-info-method) w tym artykule.

## <a name="delete-text-messaging-from-your-security-info-methods"></a>Usuwanie wiadomości tekstowych z metod informacji zabezpieczających

Jeśli nie chcesz już używać wiadomości tekstowych jako metody informacji zabezpieczających, możesz usunąć ją ze strony **informacje zabezpieczające** .

>[!Important]
>W przypadku usuwania wiadomości tekstowych przez pomyłkę nie ma możliwości cofnięcia. Musisz ponownie dodać metodę, wykonując czynności opisane w sekcji [Konfigurowanie wiadomości SMS](#set-up-text-messages-from-the-security-info-page) w tym artykule.

### <a name="to-delete-text-messaging"></a>Aby usunąć wiadomości SMS

1. Na stronie **informacje zabezpieczające** wybierz łącze **Usuń** obok opcji **telefon** .

    ![Link umożliwiający usunięcie metody obsługi wiadomości telefonu i tekstu z informacji zabezpieczających](media/security-info/securityinfo-myprofile-phonetext-delete.png)

2. Wybierz opcję **tak** w polu potwierdzenia, aby usunąć numer **telefonu** . Po usunięciu numeru telefonu zostanie on usunięty z informacji zabezpieczających i zniknie ze strony z **informacjami o zabezpieczeniach** . Jeśli **telefon** jest metodą domyślną, domyślnie zmieni się na inną dostępną metodę.

## <a name="change-your-default-security-info-method"></a>Zmień domyślną metodę informacji zabezpieczających

Jeśli chcesz, aby obsługa komunikatów tekstowych była metodą domyślną używaną podczas logowania się do konta służbowego przy użyciu weryfikacji dwuskładnikowej lub żądania resetowania hasła, możesz ustawić ją na stronie **Informacje o zabezpieczeniach** .

### <a name="to-change-your-default-security-info-method"></a>Aby zmienić domyślną metodę informacji zabezpieczających

1. Na stronie **Informacje o zabezpieczeniach** wybierz łącze **Zmień** obok domyślnych informacji o **metodzie logowania** .

    ![Zmień link dla domyślnej metody logowania](media/security-info/securityinfo-myprofile-phonetext-defaultchange.png)

2. Wybierz pozycję **telefon-tekst (*_your_phone_number_*)** z listy rozwijanej dostępne metody, a następnie wybierz pozycję **Potwierdź**.

    ![Wybierz metodę logowania domyślnego](media/security-info/securityinfo-myprofile-phonetext-changeddefault.png)

    Domyślna metoda używana do wprowadzania zmian na **telefonie (*_your_phone_number_*)**.

## <a name="additional-security-info-methods"></a>Dodatkowe metody informacji zabezpieczających

Dostępne są dodatkowe opcje dotyczące sposobu, w jaki Twoja organizacja kontaktuje się z Twoją tożsamością, w oparciu o to, co you're próbę wykonania. Dostępne są następujące opcje:

- **Aplikacja uwierzytelniania.** Pobierz aplikację Authenticator i użyj jej do uzyskania powiadomienia o zatwierdzeniu lub losowo wygenerowanego kodu zatwierdzenia na potrzeby weryfikacji dwuetapowej lub resetowania hasła. Instrukcje krok po kroku dotyczące konfigurowania i używania aplikacji Microsoft Authenticator można znaleźć w temacie [set up Security Info to use the Authenticator App](security-info-setup-auth-app.md).

- **Urządzenie przenośne lub połączenie telefoniczne.** Wprowadź numer urządzenia przenośnego i otrzymaj połączenie telefoniczne w celu weryfikacji dwuetapowej lub resetowania hasła. Aby uzyskać instrukcje krok po kroku dotyczące weryfikowania tożsamości przy użyciu numeru telefonu, zobacz [Konfigurowanie informacji zabezpieczających do korzystania z połączeń telefonicznych](security-info-setup-phone-number.md).

- **Klucz zabezpieczeń.** Zarejestruj klucz zabezpieczeń zgodny z firmą Microsoft i użyj go wraz z numerem PIN w celu weryfikacji dwuetapowej lub resetowania hasła. Instrukcje krok po kroku dotyczące weryfikowania tożsamości przy użyciu klucza zabezpieczeń znajdują się w temacie [set up Security Info to use a Security Key](security-info-setup-security-key.md).

- **Adres e-mail.** Wprowadź służbowy adres e-mail, aby uzyskać wiadomość e-mail na potrzeby resetowania hasła. Ta opcja jest niedostępna w przypadku weryfikacji dwuetapowej. Aby uzyskać instrukcje krok po kroku dotyczące konfigurowania poczty e-mail, zobacz [Konfigurowanie informacji zabezpieczających do używania poczty e-mail](security-info-setup-email.md).

- **Pytania zabezpieczające.** Odpowiedz na niektóre pytania zabezpieczające utworzone przez administratora w organizacji. Ta opcja jest dostępna tylko do resetowania haseł, a nie do weryfikacji dwuetapowej. Aby uzyskać instrukcje krok po kroku dotyczące konfigurowania pytań zabezpieczających, zobacz artykuł [Konfigurowanie informacji zabezpieczających do używania pytań zabezpieczających](security-info-setup-questions.md) .

    >[!Note]
    >Jeśli brakuje niektórych z tych opcji, prawdopodobnie organizacja nie zezwala na te metody. W takim przypadku należy wybrać dostępną metodę lub skontaktować się z administratorem w celu uzyskania pomocy.

## <a name="next-steps"></a>Następne kroki

- Zagubione lub zapomniane hasło możesz zresetować w [portalu resetowania hasła](https://passwordreset.microsoftonline.com/) lub wykonując kroki przedstawione w artykule [Resetowanie hasła służbowego](active-directory-passwords-update-your-own-password.md).

- Wskazówki i pomoc dotyczące rozwiązywania problemów z logowaniem znajdują się w artykule [Nie można zalogować się na konto Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
