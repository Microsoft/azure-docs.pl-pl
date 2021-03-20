---
title: Skonfiguruj informacje zabezpieczające na podstawie monitu logowania — Azure AD
description: Jak skonfigurować informacje zabezpieczające dla konta służbowego, po wyświetleniu monitu ze strony logowania w organizacji.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: rhicock
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 04/07/2020
ms.author: curtand
ms.openlocfilehash: 016e0de26bf3a9513940be161cfecf6d1c2b9d02
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88798503"
---
# <a name="set-up-your-security-info-from-a-sign-in-prompt"></a>Skonfiguruj informacje zabezpieczające na podstawie monitu logowania

Następujące kroki można wykonać w przypadku wyświetlenia monitu o skonfigurowanie informacji zabezpieczających natychmiast po zalogowaniu się na konto służbowe.

Ten monit będzie wyświetlany tylko wtedy, gdy nie skonfigurowano informacji zabezpieczających wymaganych przez organizację. Jeśli Twoje informacje zabezpieczające zostały już skonfigurowane wcześniej, ale chcesz wprowadzić zmiany, możesz wykonać kroki opisane w artykułach z instrukcjami dotyczącymi różnych metod. Aby uzyskać więcej informacji, zobacz [Dodawanie lub aktualizowanie informacji zabezpieczających — omówienie](./security-info-setup-auth-app.md).

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

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

## <a name="sign-in-to-your-work-or-school-account"></a>Logowanie na konto służbowe

Po zalogowaniu się na konto służbowe zobaczysz monit o podanie kolejnych informacji przed uzyskaniem dostępu do konta.

![Monit z pytaniem o kolejne informacje](media/security-info/securityinfo-prompt.png)

## <a name="set-up-your-security-info-using-the-wizard"></a>Konfigurowanie informacji zabezpieczających za pomocą kreatora

Wykonaj następujące kroki, aby skonfigurować informacje zabezpieczające dla swojego konta służbowego z monitu.

>[!Important]
>Jest to tylko przykład procesu. W zależności od wymagań organizacji administrator mógł ustawić różne metody weryfikacji, które należy skonfigurować w trakcie tego procesu. W tym przykładzie wymagamy dwóch metod: aplikacji Microsoft Authenticator i numeru telefonu komórkowego na potrzeby weryfikacyjnych rozmów telefonicznych lub wiadomości SMS.

1. Po wybraniu pozycji **Dalej** w monicie zostanie wyświetlony **kreator Zabezpiecz swoje konto**, pokazując pierwszą metodę, której skonfigurowanie jest wymagane przez administratora i organizację. W tym przykładzie jest to aplikacja Microsoft Authenticator.

   > [!Note]
   > Jeśli chcesz użyć aplikacji uwierzytelniającej innej niż aplikacja Microsoft Authenticator, wybierz link **Chcę użyć innej aplikacji uwierzytelniania**.
   >
   > Jeśli organizacja umożliwia wybranie innej metody niż aplikacja uwierzytelniająca, możesz wybrać **link Chcę skonfigurować inną metodę**.

    ![Kreator Zabezpiecz swoje konto pokazujący stronę pobierania aplikacji uwierzytelniającej](media/security-info/securityinfo-prompt-get-auth-app.png)

2. Wybierz pozycję **Pobierz teraz**, aby pobrać i zainstalować aplikację Microsoft Authenticator na swoim urządzeniu mobilnym, a następnie wybierz pozycję **Dalej**. Aby uzyskać więcej informacji o pobieraniu i instalowaniu aplikacji, zobacz [Pobieranie i instalowanie aplikacji Microsoft Authenticator](user-help-auth-app-download-install.md).

    ![Kreator Zabezpiecz swoje konto pokazujący stronę konfigurowania konta programu uwierzytelniającego](media/security-info/securityinfo-prompt-auth-app-setup-acct.png)

3. Pozostań na stronie **Konfigurowanie konta** podczas konfigurowania aplikacji Microsoft Authenticator na urządzeniu mobilnym.

4. Otwórz aplikację Microsoft Authenticator, zezwól na powiadomienia (jeśli pojawi się monit), wybierz pozycję **Dodaj konto** z menu ikony **Dostosowywanie i kontrolowanie** w prawym górnym rogu, a następnie wybierz pozycję **Konto służbowe**.

    >[!Note]
    >Jeśli konfigurujesz aplikację Microsoft Authenticator po raz pierwszy, może zostać wyświetlony monit z pytaniem, czy zezwolić aplikacji na dostęp do aparatu (iOS), czy zezwolić aplikacji na wykonywanie zdjęć i nagrywanie wideo (Android). Musisz wybrać opcję **Zezwól** , aby aplikacja Authenticator mogła uzyskać dostęp do Twojego aparatu, aby w następnym kroku uzyskać zdjęcie kodu QR. Jeśli nie zezwolisz na korzystanie z aparatu, nadal możesz skonfigurować aplikację uwierzytelniającej, ale musisz ręcznie dodać informacje o kodzie. Aby dowiedzieć się, jak ręcznie dodać kod, zobacz temat [Ręczne dodawanie konta do aplikacji](user-help-auth-app-add-account-manual.md).

5. Wróć na stronę **Konfigurowanie konta** na komputerze, a następnie wybierz pozycję **Dalej**.

    Zostanie wyświetlona strona **Skanowanie kodu QR**.

    ![Skanowanie kodu QR przy użyciu aplikacji Authenticator](media/security-info/securityinfo-prompt-auth-app-qrcode.png)

6. Zeskanuj podany kod przy użyciu czytnika kodów QR aplikacji Microsoft Authenticator, który pojawił się na urządzeniu mobilnym po utworzeniu konta służbowego w kroku 5.

    Aplikacja Authenticator powinna pomyślnie dodać konto służbowe bez konieczności podawania żadnych dodatkowych informacji przez użytkownika. Jeśli jednak czytnik kodu QR nie może odczytać kodu, możesz wybrać opcję **nie można zeskanować obrazu QR** i ręcznie wprowadzić kod i adres URL do aplikacji Microsoft Authenticator. Aby uzyskać więcej informacji na temat ręcznego dodawania kodu, zobacz [Ręcznie dodawanie konta do aplikacji](user-help-auth-app-add-account-manual.md).

7. Wybierz pozycję **Dalej** na stronie **Zeskanuj kod QR** na komputerze.

    Do aplikacji Microsoft Authenticator na urządzeniu przenośnym zostanie wysłane powiadomienie o konieczności przetestowania konta.

    ![Testowanie konta za pomocą aplikacji Authenticator](media/security-info/securityinfo-prompt-test-app.png)

8. Zatwierdź powiadomienie w aplikacji Microsoft Authenticator, a następnie wybierz pozycję **Dalej**.

    ![Powiadomienie o powodzeniu łączenia aplikacji z kontem](media/security-info/securityinfo-prompt-auth-app-success.png)

    Informacje zabezpieczające zostały zaktualizowane, tak aby aplikacja Microsoft Authenticator była domyślnie używana do weryfikowania tożsamości podczas korzystania z weryfikacji dwuetapowej lub resetowania hasła.

9. Na stronie konfiguracji **Telefon** określ, czy chcesz otrzymywać wiadomości SMS, czy odbierać rozmowy telefoniczne, a następnie wybierz pozycję **Dalej**. Na potrzeby tego przykładu używamy wiadomości SMS, więc należy użyć numeru telefonu urządzenia, które może przyjmować wiadomości SMS.

    ![Rozpoczynanie konfigurowania numeru telefonu dla wiadomości SMS](media/security-info/securityinfo-prompt-text-msg.png)

    Wiadomości SMS są wysyłane na numer telefonu. Jeśli chcesz korzystać z rozmów telefonicznych, proces jest taki sam. Będziesz jednak otrzymywać rozmowy telefoniczne z instrukcjami zamiast wiadomości SMS.

10. Podaj kod określony w wiadomości SMS wysłanej na urządzenie przenośne, a następnie wybierz pozycję **Dalej**.

    ![Testowanie konta za pomocą wiadomości SMS](media/security-info/securityinfo-prompt-text-msg-enter-code.png)

11. Przejrzyj powiadomienie o powodzeniu, a następnie wybierz pozycję **Gotowe**.

    ![Powiadomienie o powodzeniu](media/security-info/securityinfo-prompt-call-answered-success.png)

    Informacje zabezpieczające zostały zaktualizowane, tak aby wiadomości SMS były używane jako zapasowa metoda weryfikowania tożsamości podczas korzystania z weryfikacji dwuetapowej lub resetowania hasła.

12. Przejrzyj stronę **Powodzenie**, aby zweryfikować, czy aplikacja Microsoft Authenticator i metoda korzystająca z telefonu (wiadomości SMS lub rozmowy telefonicznej) zostały skonfigurowane w ramach informacji zabezpieczających, a następnie wybierz pozycję **Gotowe**.

    ![Strona pomyślnego zakończenia kreatora](media/security-info/securityinfo-prompt-setup-success.png)

    >[!Note]
    >Jeśli Twoja organizacja wymaga używania haseł aplikacji, w tym kreatorze może zostać wyświetlona dodatkowa sekcja, w której można ją skonfigurować. Jeśli zobaczysz trzecią sekcję o nazwie **hasła aplikacji**, musisz wypełnić ją, zanim będzie można zakończyć pracę kreatora. Instrukcje dotyczące sposobu dodawania hasła aplikacji znajdują się w sekcji [Zarządzanie hasłami aplikacji](#manage-your-app-passwords) w tym artykule.

### <a name="manage-your-app-passwords"></a>Zarządzanie hasłami aplikacji

Niektóre aplikacje, takie jak Outlook 2010, nie obsługują weryfikacji dwuetapowej. Brak pomocy technicznej oznacza, że jeśli korzystasz z weryfikacji dwuetapowej w organizacji, aplikacja nie będzie działała. Aby obejść ten problem, możesz utworzyć automatycznie generowane hasło, które będzie używane z każdą aplikacją nie korzystającą z przeglądarki, niezależnie od normalnego hasła.

>[!Note]
>Jeśli ta opcja nie jest widoczna w kreatorze, oznacza to, że administrator nie skonfigurował tego ustawienia. Jeśli nie skonfigurowano tego ustawienia, ale wiesz, że musisz używać haseł aplikacji, możesz wykonać czynności opisane w sekcji [Konfigurowanie haseł aplikacji ze strony informacje zabezpieczające](security-info-app-passwords.md).

W przypadku korzystania z haseł aplikacji ważne jest, aby pamiętać:

- Hasła aplikacji są generowane automatycznie i wprowadzane tylko raz dla każdej aplikacji.

- Dla każdego użytkownika istnieje limit 40 haseł. Jeśli spróbujesz utworzyć jeden po tym limicie, zostanie wyświetlony monit o usunięcie istniejącego hasła przed utworzeniem nowego.

- Użyj jednego hasła aplikacji na urządzenie, a nie na aplikację. Na przykład utwórz jedno hasło dla wszystkich aplikacji na laptopie, a następnie inne pojedyncze hasło dla wszystkich aplikacji na pulpicie.

#### <a name="to-add-app-passwords-in-the-sign-in-wizard"></a>Aby dodać hasła aplikacji w Kreatorze logowania

1. Po zakończeniu poprzednich sekcji kreatora wybierz pozycję **dalej** i wypełnij sekcję **hasło aplikacji** .

2. Wpisz nazwę aplikacji, która wymaga hasła, na przykład `Outlook 2010` , a następnie wybierz przycisk **dalej**.

    ![Dodaj nazwę hasła aplikacji w Kreatorze](media/security-info/app-password-app-password.png)

3. Skopiuj kod hasła z ekranu **hasła aplikacji** i wklej go do obszaru **hasło** aplikacji (w tym przykładzie w programie Outlook 2010).

    ![Strona hasła aplikacji z hasłem do kopiowania](media/security-info/app-password-copy-password.png)

4. Po skopiowaniu hasła i wklejeniu go w aplikacji Wróć do tego kreatora, aby upewnić się, że wszystkie informacje o metodzie logowania są prawidłowe, a następnie wybierz pozycję **gotowe**.

    ![Strona hasła aplikacji z powiadomieniem o zakończeniu](media/security-info/app-password-complete.png)

## <a name="next-steps"></a>Następne kroki

- Aby zmienić, usunąć lub zaktualizować domyślne metody informacji zabezpieczających, zobacz:

    - [Konfigurowanie informacji zabezpieczających dla aplikacji Authenticator](security-info-setup-auth-app.md).

    - [Skonfiguruj informacje zabezpieczające dla wiadomości tekstowych](security-info-setup-text-msg.md).

    - [Skonfiguruj informacje zabezpieczające, aby korzystać z połączeń telefonicznych](security-info-setup-phone-number.md).

    - [Skonfiguruj informacje zabezpieczające do korzystania z poczty e-mail](security-info-setup-email.md).

    - [Skonfiguruj informacje zabezpieczające, aby korzystać ze wstępnie zdefiniowanych pytań zabezpieczających](security-info-setup-questions.md).

- Aby uzyskać informacje o logowaniu za pomocą określonej metody, zobacz [Jak się zalogować](user-help-sign-in.md).

- Zagubione lub zapomniane hasło możesz zresetować w [portalu resetowania hasła](https://passwordreset.microsoftonline.com/) lub wykonując kroki przedstawione w artykule [Resetowanie hasła służbowego](active-directory-passwords-update-your-own-password.md).

- Wskazówki i pomoc dotyczące rozwiązywania problemów z logowaniem znajdują się w artykule [Nie można zalogować się na konto Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).