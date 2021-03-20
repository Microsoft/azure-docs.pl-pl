---
title: Konfigurowanie adresu e-mail jako metody weryfikacji — Azure AD
description: Jak skonfigurować informacje zabezpieczające (wersja zapoznawcza) w celu zweryfikowania tożsamości przy użyciu adresu e-mail jako metody weryfikacji.
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
ms.openlocfilehash: 4f0ea55149fc8cfeeb6ef719d67346019b81078a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "83744471"
---
# <a name="set-up-an-email-address-as-your-verification-method"></a>Skonfiguruj adres e-mail jako metodę weryfikacji

Poniższe kroki umożliwiają dodanie metody resetowania hasła. Po wybraniu tej opcji po raz pierwszy możesz wrócić do strony **informacje zabezpieczające** , aby dodać, zaktualizować lub usunąć informacje o zabezpieczeniach.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Jeśli nie widzisz opcji poczty e-mail, być może Twoja organizacja nie zezwala na korzystanie z tej opcji w przypadku metody resetowania hasła. W takim przypadku należy wybrać inną metodę lub skontaktować się z działem pomocy technicznej w organizacji, aby uzyskać pomoc.

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

## <a name="set-up-your-email-address-from-the-security-info-page"></a>Konfigurowanie adresu e-mail na stronie z informacjami o zabezpieczeniach

W zależności od ustawień organizacji może być możliwe użycie adresu e-mail jako jednej z metod informacji zabezpieczających.

>[!Note]
>Zalecamy użycie adresu e-mail, który nie wymaga hasła do sieci, aby uzyskać dostęp do programu. Jeśli nie widzisz opcji poczty e-mail, być może Twoja organizacja nie zezwala na weryfikację za pomocą wiadomości e-mail. W takim przypadku należy wybrać inną metodę lub skontaktować się z administratorem w celu uzyskania dodatkowej pomocy.

### <a name="to-set-up-your-email-address"></a>Aby skonfigurować swój adres e-mail

1. Zaloguj się do swojego konta służbowego, a następnie przejdź do https://myaccount.microsoft.com/ strony.

    ![Strona mój profil zawierająca wyróżnione linki do informacji zabezpieczających](media/security-info/securityinfo-myprofile-page.png)

2. W okienku nawigacji po lewej stronie wybierz pozycję **informacje zabezpieczające** lub link w bloku **informacje zabezpieczające** , a następnie wybierz pozycję **Dodaj metodę** ze strony **informacje zabezpieczające** .

    ![Strona z informacjami o zabezpieczeniach z wyróżnioną opcją dodawania metody](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na stronie **Dodawanie metody** wybierz z listy rozwijanej pozycję **poczta e-mail** , a następnie wybierz pozycję **Dodaj**.

    ![Dodaj pole metody z wybraną pocztą e-mail](media/security-info/securityinfo-myprofile-addemail.png)

4. Na stronie **poczta e-mail** wpisz swój adres e-mail (na przykład alain@gmail.com ), a następnie wybierz przycisk **dalej**.

    ![Dodawanie numeru telefonu i Wybieranie połączeń telefonicznych](media/security-info/securityinfo-myprofile-emailaddress.png)

    >[!Important]
    >Ten adres e-mail nie może być służbowym adresem e-mail.

5. Wpisz kod wysłany na określony adres e-mail, a następnie wybierz przycisk **dalej**.

    ![Dodawanie numeru telefonu i wybieranie wiadomości SMS](media/security-info/securityinfo-myprofile-emailcode.png)

    Informacje zabezpieczające zostały zaktualizowane i można użyć swojego adresu e-mail do zweryfikowania tożsamości podczas korzystania z funkcji resetowania haseł.

## <a name="delete-your-email-address-from-your-security-info-methods"></a>Usuń swój adres e-mail z metod informacji zabezpieczających

Jeśli nie chcesz już używać adresu e-mail jako metody informacji zabezpieczających, możesz usunąć ją ze strony **informacje zabezpieczające** .

>[!Important]
>Jeśli usuniesz adres e-mail przez pomyłkę, nie ma możliwości jego cofnięcia. Musisz ponownie dodać metodę, wykonując czynności opisane w sekcji [Konfigurowanie adresu e-mail](#set-up-your-email-address-from-the-security-info-page) w tym artykule.

### <a name="to-delete-your-email-address"></a>Aby usunąć swój adres e-mail

1. Na stronie **informacje zabezpieczające** wybierz łącze **Usuń** obok opcji **wiadomości e-mail** .

    ![Link umożliwiający usunięcie metody telefonu z informacji zabezpieczających](media/security-info/securityinfo-myprofile-emaildelete.png)

2. Wybierz opcję **tak** w polu potwierdzenia, aby usunąć konto **e-mail** . Po usunięciu konta e-mail zostanie ono usunięte z informacji zabezpieczających i zniknie ze strony z **informacjami o zabezpieczeniach** .

## <a name="additional-security-info-methods"></a>Dodatkowe metody informacji zabezpieczających

Dostępne są dodatkowe opcje dotyczące sposobu, w jaki Twoja organizacja kontaktuje się z Twoją tożsamością, w oparciu o to, co you're próbę wykonania. Dostępne są następujące opcje:

- **Aplikacja uwierzytelniania.** Pobierz aplikację Authenticator i użyj jej do uzyskania powiadomienia o zatwierdzeniu lub losowo wygenerowanego kodu zatwierdzenia na potrzeby weryfikacji dwuetapowej lub resetowania hasła. Instrukcje krok po kroku dotyczące konfigurowania i używania aplikacji Microsoft Authenticator można znaleźć w temacie [set up Security Info to use the Authenticator App](security-info-setup-auth-app.md).

- **Tekst urządzenia przenośnego.** Wprowadź numer urządzenia przenośnego i uzyskaj tekst kodu, który będzie używany do weryfikacji dwuetapowej lub resetowania hasła. Aby uzyskać instrukcje krok po kroku dotyczące weryfikowania tożsamości za pomocą wiadomości tekstowych (SMS), zobacz [Konfigurowanie informacji zabezpieczających do używania wiadomości](security-info-setup-text-msg.md)SMS.

- **Urządzenie przenośne lub połączenie telefoniczne.** Wprowadź numer urządzenia przenośnego i otrzymaj połączenie telefoniczne w celu weryfikacji dwuetapowej lub resetowania hasła. Aby uzyskać instrukcje krok po kroku dotyczące weryfikowania tożsamości przy użyciu numeru telefonu, zobacz [Konfigurowanie informacji zabezpieczających do korzystania z połączeń telefonicznych](security-info-setup-phone-number.md).

- **Klucz zabezpieczeń.** Zarejestruj klucz zabezpieczeń zgodny z firmą Microsoft i użyj go wraz z numerem PIN w celu weryfikacji dwuetapowej lub resetowania hasła. Instrukcje krok po kroku dotyczące weryfikowania tożsamości przy użyciu klucza zabezpieczeń znajdują się w temacie [set up Security Info to use a Security Key](security-info-setup-security-key.md).

- **Pytania zabezpieczające.** Odpowiedz na niektóre pytania zabezpieczające utworzone przez administratora w organizacji. Ta opcja jest dostępna tylko do resetowania haseł, a nie do weryfikacji dwuetapowej. Aby uzyskać instrukcje krok po kroku dotyczące konfigurowania pytań zabezpieczających, zobacz artykuł [Konfigurowanie informacji zabezpieczających do używania pytań zabezpieczających](security-info-setup-questions.md) .

    >[!Note]
    >Jeśli brakuje niektórych z tych opcji, prawdopodobnie organizacja nie zezwala na te metody. W takim przypadku należy wybrać dostępną metodę lub skontaktować się z administratorem w celu uzyskania pomocy.

## <a name="next-steps"></a>Następne kroki

- Zagubione lub zapomniane hasło możesz zresetować w [portalu resetowania hasła](https://passwordreset.microsoftonline.com/) lub wykonując kroki przedstawione w artykule [Resetowanie hasła służbowego](active-directory-passwords-update-your-own-password.md).

- Wskazówki i pomoc dotyczące rozwiązywania problemów z logowaniem znajdują się w artykule [Nie można zalogować się na konto Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
