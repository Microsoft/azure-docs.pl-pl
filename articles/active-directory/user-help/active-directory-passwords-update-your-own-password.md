---
title: Zresetuj hasło przy użyciu informacji zabezpieczających — Azure Active Directory | Microsoft Docs
description: Jak zresetować własne hasło, jeśli go zapomnisz, przy użyciu informacji zabezpieczających i weryfikacji dwuetapowej.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 09/10/2020
ms.author: curtand
ms.openlocfilehash: c47d573dab3e72a956f6f0b9d09a1dafadf0225c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91827774"
---
# <a name="reset-your-work-or-school-password-using-security-info"></a>Zresetuj hasło służbowe przy użyciu informacji zabezpieczających

Jeśli nie pamiętasz hasła służbowego, nigdy nie otrzymasz hasła z Twojej organizacji lub nie masz zablokowanego konta, możesz użyć informacji zabezpieczających i urządzenia przenośnego, aby zresetować hasło służbowe. Administrator musi włączyć tę funkcję, aby móc skonfigurować informacje i [zresetować własne hasło](./active-directory-passwords-reset-register.md).

Jeśli znasz hasło i chcesz je zmienić, zapoznaj się z sekcjami [zmiana hasła](#how-to-change-your-password) w tym artykule.

>[!Important]
>Ten artykuł jest przeznaczony dla użytkowników próbujących zresetować zapomniane lub nieznane hasło konta służbowego. Jeśli jesteś administratorem, aby uzyskać informacje na temat włączania samoobsługowego resetowania haseł dla pracowników lub innych użytkowników, zobacz Wdrażanie funkcji samoobsługowego [resetowania haseł w usłudze Azure AD i innych artykułów](../authentication/howto-sspr-deployment.md).

## <a name="how-to-reset-or-unlock-your-password-for-a-work-or-school-account"></a>Jak zresetować lub odblokować hasło dla konta służbowego

Jeśli nie możesz uzyskać dostępu do konta usługi Azure Active Directory (Azure AD), może to być spowodowane tym, że:

- Twoje hasło nie działa i chcesz je zresetować.

- Znasz hasło, ale Twoje konto jest zablokowane i musisz je odblokować.

### <a name="to-reset-your-password-and-get-back-into-your-account"></a>Aby zresetować hasło i wrócić do swojego konta

1. Na ekranie **wprowadzanie hasła** wybierz opcję nie **pamiętam hasła**.

2. Na ekranie **Wróć do konta** wpisz swój **Identyfikator użytkownika** lub szkoły (na przykład Twój adres e-mail), aby udowodnić, że nie jesteś robotem, wprowadzając znaki widoczne na ekranie, a następnie wybierz przycisk **dalej**.

   ![Wróć do ekranu konta](media/security-info/security-info-back-into-acct.png)

   >[!NOTE]
   >Jeśli administrator nie włączył możliwości resetowania własnego hasła, zobaczysz link **skontaktuj się z administratorem** zamiast przycisku **Wróć do konta** . Ten link umożliwia skontaktowanie się z administratorem w sprawie resetowania hasła za pośrednictwem poczty e-mail lub portalu sieci Web.

3. Wybierz jedną z następujących metod, aby zweryfikować swoją tożsamość i zmienić hasło. W zależności od tego, jak administrator skonfigurował Twoją organizację, może być konieczne przeprowadzenie tego procesu po raz drugi, dodanie informacji dla drugiego kroku weryfikacji.

    ![Wróć do swojego konta, #1 kroku weryfikacji](media/security-info/security-info-back-into-acct2.png)

    >[!NOTE]
    >W zależności od tego, jak administrator skonfigurował Twoją organizację, niektóre z tych opcji weryfikacyjnych mogą być niedostępne. Wcześniej należy skonfigurować urządzenie przenośne do weryfikacji przy użyciu co najmniej jednej z tych metod.<br><br>Ponadto nowe hasło może wymagać spełnienia pewnych wymagań dotyczących siły. Silne hasła zwykle zawierają od 8 do 16 znaków, w tym wielkie i małe litery, co najmniej jedną cyfrę oraz co najmniej jeden znak specjalny.

- **Zresetuj hasło przy użyciu adresu e-mail.** Wysyła wiadomość e-mail na adres e-mail, który został wcześniej skonfigurowany w ramach weryfikacji dwuetapowej lub informacji zabezpieczających. Jeśli administrator włączył środowisko informacje zabezpieczające, można znaleźć więcej informacji na temat konfigurowania adresu e-mail w artykule [Konfigurowanie informacji zabezpieczających do używania poczty e-mail (wersja zapoznawcza)](security-info-setup-email.md) . Jeśli jeszcze nie korzystasz z informacji zabezpieczających, możesz znaleźć więcej informacji na temat konfigurowania adresu e-mail w artykule [Konfigurowanie mojego konta na potrzeby weryfikacji dwuetapowej](multi-factor-authentication-end-user-first-time.md) . 

    1. Wybierz pozycję **Wyślij pocztą e-mail mój alternatywny adres e-mail**, a następnie wybierz pozycję **poczta e-mail**.

    2. Wpisz kod weryfikacyjny z wiadomości e-mail w polu, a następnie wybierz przycisk **dalej**.

    3. Wpisz i Potwierdź nowe hasło, a następnie wybierz pozycję **Zakończ**.

- **Zresetuj hasło przy użyciu wiadomości tekstowej.** Wysyła wiadomość SMS na numer telefonu, który został wcześniej skonfigurowany w informacjach zabezpieczających. Jeśli administrator włączył środowisko informacje zabezpieczające, można znaleźć więcej informacji na temat konfigurowania wiadomości tekstowych w artykule [Konfigurowanie informacji zabezpieczających do korzystania z wiadomości tekstowych (wersja zapoznawcza)](security-info-setup-text-msg.md) . Jeśli jeszcze nie korzystasz z informacji zabezpieczających, możesz znaleźć więcej informacji na temat konfigurowania wiadomości tekstowych w artykule [Konfigurowanie mojego konta na potrzeby weryfikacji dwuetapowej](multi-factor-authentication-end-user-first-time.md) .

    1. Wybierz pozycję **tekst mój telefon komórkowy**, wpisz swój numer telefonu, a następnie wybierz pozycję **tekst**.

    2. Wpisz kod weryfikacyjny z wiadomości tekstowej w polu, a następnie wybierz przycisk **dalej**.

    3. Wpisz i Potwierdź nowe hasło, a następnie wybierz pozycję **Zakończ**.

- **Zresetuj hasło, używając numeru telefonu.** Umieszcza automatyczne połączenie głosowe z wcześniej skonfigurowanym numerem telefonu w informacjach zabezpieczających. Jeśli administrator włączył środowisko informacje zabezpieczające, można znaleźć więcej informacji na temat konfigurowania numeru telefonu w artykule [Konfigurowanie informacji zabezpieczających do korzystania z połączenia telefonicznego (wersja zapoznawcza)](security-info-setup-phone-number.md) . Jeśli jeszcze nie korzystasz z informacji zabezpieczających, możesz znaleźć więcej informacji na temat konfigurowania numeru telefonu w artykule [Konfigurowanie mojego konta na potrzeby weryfikacji dwuetapowej](multi-factor-authentication-end-user-first-time.md) .

    1. Wybierz pozycję **Zadzwoń na mój telefon komórkowy**, wpisz numer telefonu, a następnie wybierz pozycję **Połącz**.

    2. Odpowiedz na połączenie telefoniczne i postępuj zgodnie z instrukcjami, aby zweryfikować swoją tożsamość, a następnie wybierz przycisk **dalej**.

    3. Wpisz i Potwierdź nowe hasło, a następnie wybierz pozycję **Zakończ**.

- **Zresetuj hasło, korzystając z pytań zabezpieczających.** Zawiera listę pytań zabezpieczających skonfigurowanych w oknie Informacje zabezpieczające. Jeśli administrator włączył środowisko z informacjami o zabezpieczeniach, możesz znaleźć więcej informacji na temat konfigurowania pytań zabezpieczających w artykule [Konfigurowanie informacji zabezpieczających, aby korzystać ze wstępnie zdefiniowanych pytań zabezpieczających (wersja zapoznawcza)](security-info-setup-questions.md) . Jeśli jeszcze nie korzystasz z informacji zabezpieczających, możesz znaleźć więcej informacji na temat konfigurowania pytań zabezpieczających w artykule [Konfigurowanie mojego konta na potrzeby weryfikacji dwuetapowej](multi-factor-authentication-end-user-first-time.md) .

    1. Wybierz pozycję **odpowiedzi moje pytania zabezpieczające**, Odpowiedz na pytania, a następnie wybierz przycisk **dalej**.

    2. Wpisz i Potwierdź nowe hasło, a następnie wybierz pozycję **Zakończ**.

- **Zresetuj hasło przy użyciu powiadomienia z aplikacji uwierzytelniania.** Wysyła powiadomienie o zatwierdzeniu do aplikacji uwierzytelniania. Jeśli administrator włączył środowisko informacje zabezpieczające, można znaleźć więcej informacji na temat konfigurowania aplikacji uwierzytelniającej w celu wysłania powiadomienia w artykule [Konfigurowanie informacji zabezpieczających do korzystania z aplikacji uwierzytelniania (wersja zapoznawcza)](security-info-setup-auth-app.md) . Jeśli jeszcze nie korzystasz z informacji zabezpieczających, możesz znaleźć więcej informacji na temat konfigurowania aplikacji uwierzytelniającej w celu wysłania powiadomienia w artykule [Konfigurowanie mojego konta na potrzeby weryfikacji dwuetapowej](multi-factor-authentication-end-user-first-time.md) .

    1. Wybierz pozycję **Zatwierdź powiadomienie w mojej aplikacji uwierzytelniającej**, a następnie wybierz pozycję **Wyślij powiadomienie**.

    2. Zatwierdź logowanie z poziomu aplikacji uwierzytelniania.

    3. Wpisz i Potwierdź nowe hasło, a następnie wybierz pozycję **Zakończ**.

- **Zresetuj hasło przy użyciu kodu z aplikacji uwierzytelniania.** Akceptuje losowy kod dostarczany przez aplikację uwierzytelniania. Jeśli administrator włączył środowisko z informacjami o zabezpieczeniach, możesz znaleźć więcej informacji na temat konfigurowania aplikacji uwierzytelniającej, aby udostępnić kod w artykule [Konfigurowanie informacji zabezpieczających do korzystania z aplikacji uwierzytelniania (wersja zapoznawcza)](security-info-setup-auth-app.md) . Jeśli jeszcze nie korzystasz z informacji zabezpieczających, możesz znaleźć więcej informacji na temat konfigurowania aplikacji uwierzytelniającej, aby udostępnić kod w artykule [Konfigurowanie mojego konta na potrzeby weryfikacji dwuetapowej](multi-factor-authentication-end-user-first-time.md) .

  1. Wybierz pozycję **Wprowadź kod z aplikacji Authenticator**, a następnie wybierz pozycję **Wyślij powiadomienie**.

  2. Otwórz aplikację Authenticator, wpisz kod weryfikacyjny dla swojego konta w polu, a następnie wybierz przycisk **dalej**.

  3. Wpisz i Potwierdź nowe hasło, a następnie wybierz pozycję **Zakończ**.

  4. Po otrzymaniu komunikatu z informacją, że hasło zostało zresetowane, możesz zalogować się do swojego konta przy użyciu nowego hasła.

     Jeśli nadal nie możesz uzyskać dostępu do konta, skontaktuj się z administratorem organizacji, aby uzyskać pomoc.

Po zresetowaniu hasła może zostać wykorzystana wiadomość e-mail z potwierdzeniem, która pochodzi z konta takiego jak "Microsoft w imieniu" \<*your_organization*> . Jeśli otrzymasz podobną wiadomość e-mail, ale ostatnio nie zresetujesz hasła, musisz natychmiast skontaktować się z administratorem w organizacji.

## <a name="how-to-change-your-password"></a>How to change your password (Jak zmienić hasło)

Jeśli chcesz tylko zmienić hasło, możesz to zrobić za pomocą portalu pakietu Office 365, portalu Moje aplikacje lub strony logowania systemu Windows 10.

### <a name="to-change-your-password-using-the-office-365-portal"></a>Aby zmienić hasło przy użyciu portalu pakietu Office 365

Użyj tej metody, jeśli zwykle uzyskujesz dostęp do aplikacji za pomocą portalu pakietu Office:

1. Zaloguj się do [konta Office 365](https://portal.office.com)przy użyciu istniejącego hasła.

2. Wybierz swój profil w prawym górnym rogu, a następnie wybierz pozycję **Wyświetl konto**.

3. Wybierz pozycję **zabezpieczenia & hasło ochrony prywatności**  >  **Password**.

4. Wpisz stare hasło, Utwórz i Potwierdź nowe hasło, a następnie wybierz pozycję **Prześlij**.

### <a name="to-change-your-password-from-the-my-apps-portal"></a>Aby zmienić hasło w portalu My Apps

Użyj tej metody, jeśli zwykle uzyskujesz dostęp do aplikacji z portalu My Apps:

1. Zaloguj się do [portalu My Apps](https://myapps.microsoft.com/)przy użyciu istniejącego hasła.

2. Wybierz swój profil w prawym górnym rogu, a następnie wybierz pozycję **profil**.

3. Wybierz pozycję **Zmień hasło**.

4. Wpisz stare hasło, Utwórz i Potwierdź nowe hasło, a następnie wybierz pozycję **Prześlij**.

### <a name="to-change-your-password-at-windows-sign-in"></a>Aby zmienić hasło podczas logowania do systemu Windows

Jeśli administrator włączył tę funkcję, zobaczysz link służący do **resetowania hasła** na ekranie logowania systemu Windows 7, Windows 8, Windows 8.1 lub Windows 10.

1. Wybierz link **resetowania hasła** , aby rozpocząć proces resetowania hasła bez konieczności korzystania z normalnego środowiska internetowego.

2. Potwierdź swój identyfikator użytkownika i wybierz pozycję **dalej**.

3. Wybierz i Potwierdź metodę kontaktu dla weryfikacji. W razie potrzeby wybierz drugą opcję weryfikacji inną niż poprzednia, wypełniając odpowiednie informacje.

4. Na stronie **Utwórz nowe hasło** wpisz i Potwierdź nowe hasło, a następnie wybierz przycisk **dalej**.

    Silne hasła zwykle zawierają od 8 do 16 znaków, w tym wielkie i małe litery, co najmniej jedną cyfrę oraz co najmniej jeden znak specjalny.

5. Po otrzymaniu komunikatu z informacją, że hasło zostało zresetowane, możesz wybrać pozycję **Zakończ**.

    Jeśli nadal nie możesz uzyskać dostępu do konta, skontaktuj się z administratorem organizacji, aby uzyskać pomoc.

## <a name="common-problems-and-their-solutions"></a>Typowe problemy i ich rozwiązania

Poniżej przedstawiono kilka typowych przypadków błędów i ich rozwiązań:

|Problem|Opis|Rozwiązanie|
| --- | --- | --- |
|Gdy próbuję zmienić moje hasło, otrzymuję komunikat o błędzie. |Hasło zawiera słowo, frazę lub wzorzec, które ułatwiają odgadnięcie hasła.| Spróbuj ponownie, używając silniejszego hasła.|
|Po wprowadzeniu identyfikatora użytkownika przejdź na stronę o nazwie "Skontaktuj się z administratorem".|Firma Microsoft ustaliła, że hasło konta użytkownika jest zarządzane przez administratora w środowisku lokalnym. W związku z tym nie można zresetować hasła z linku "nie można uzyskać dostępu do konta". |Aby uzyskać pomoc, skontaktuj się z administratorem.|
|Po wprowadzeniu identyfikatora użytkownika otrzymuję komunikat o błędzie "Twoje konto nie jest włączone do resetowania hasła".|Administrator nie skonfigurował Twojego konta, aby można było zresetować własne hasło.|Administrator nie włączył resetowania hasła dla Twojej organizacji z poziomu linku "nie można uzyskać dostępu do konta" lub nie ma licencji na korzystanie z tej funkcji.<br><br> Aby zresetować hasło, musisz wybrać link "Skontaktuj się z administratorem", aby wysłać wiadomość e-mail do administratora firmy i poinformować o konieczności zresetowania hasła.|
|Po wprowadzeniu identyfikatora użytkownika otrzymuję komunikat o błędzie "nie można zweryfikować Twojego konta".|Proces logowania nie może zweryfikować informacji o koncie.|Istnieją dwa powody, dla których zobaczysz ten komunikat.<br><br>1. administrator włączył Resetowanie hasła dla Twojej organizacji, ale nie zarejestrowano go do korzystania z usługi. Aby zarejestrować się w celu zresetowania hasła, zapoznaj się z jednym z poniższych artykułów w oparciu o metodę weryfikacji: [Skonfiguruj informacje zabezpieczające, aby użyć aplikacji Authenticator (wersja zapoznawcza)](security-info-setup-auth-app.md), [skonfigurować informacje zabezpieczające do korzystania z połączenia telefonicznego (wersja zapoznawcza)](security-info-setup-phone-number.md), [skonfigurować informacje zabezpieczające do korzystania z wiadomości SMS (wersja](security-info-setup-text-msg.md)zapoznawcza), [skonfigurować informacje zabezpieczające do korzystania z poczty e-mail (wersja zapoznawcza)](security-info-setup-email.md)lub [skonfigurować informacje zabezpieczające w celu](security-info-setup-questions.md)<br><br>2. Administrator nie włączył resetowania hasła dla Twojej organizacji. W takiej sytuacji należy wybrać opcję "Skontaktuj się z administratorem", aby wysłać wiadomość e-mail do administratora, z prośbą o zresetowanie hasła.|

## <a name="next-steps"></a>Następne kroki

- Informacje o zabezpieczeniach w artykule [Przegląd informacji zabezpieczających (wersja zapoznawcza)](./security-info-setup-signin.md) .

- Jeśli próbujesz wrócić do konta osobistego, takiego jak Xbox, hotmail.com lub outlook.com, wypróbuj sugestie w programie, [gdy nie możesz zalogować się do konto Microsoft artykułu](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
