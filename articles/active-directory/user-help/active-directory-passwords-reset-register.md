---
title: Rejestrowanie informacji o uwierzytelnianiu w celu zresetowania własnego hasła — Azure AD
description: Zarejestruj informacje o metodzie weryfikacji dla funkcji samoobsługowego resetowania hasła usługi Azure AD, aby można było zresetować własne hasło bez pomocy administratora.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 05/28/2020
ms.author: curtand
ms.openlocfilehash: e460f3fd383286b1e61a979b87a9dfa09f272313
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88799030"
---
# <a name="register-your-verification-method-info-to-reset-your-own-password"></a>Zarejestruj informacje o metodzie weryfikacji, aby zresetować własne hasło

Jeśli nie pamiętasz hasła służbowego, nigdy nie otrzymasz hasła z Twojej organizacji lub nie masz zablokowanego konta, możesz użyć informacji zabezpieczających i urządzenia przenośnego, aby zresetować hasło służbowe.

Administrator musi włączyć tę funkcję, aby móc zarejestrować informacje i zresetować własne hasło. Jeśli nie widzisz opcji nie **pamiętam hasła** , oznacza to, że administrator nie włączył funkcji dla Twojej organizacji. Jeśli uważasz, że jest to nieprawidłowe, skontaktuj się z działem pomocy technicznej w celu uzyskania pomocy.

>[!Important]
>Ten artykuł jest przeznaczony dla użytkowników próbujących korzystać z rejestracji w celu samoobsługowego resetowania hasła. Oznacza to, że możesz zresetować własne hasło służbowe (np alain@contoso.com .), nie wymagając pomocy administratora. Jeśli jesteś administratorem, aby uzyskać informacje na temat włączania samoobsługowego resetowania haseł dla pracowników lub innych użytkowników, zobacz Wdrażanie funkcji samoobsługowego [resetowania haseł w usłudze Azure AD i innych artykułów](../authentication/howto-sspr-deployment.md).

## <a name="set-up-your-password-reset-verification-method"></a>Konfigurowanie metody weryfikacji resetowania hasła

1. Otwórz przeglądarkę internetową na urządzeniu i przejdź do [strony informacje zabezpieczające](https://account.activedirectory.windowsazure.com/PasswordReset/Register.aspx?regref=ssprsetup).

2. W zależności od sposobu skonfigurowania organizacji przez administratora można skonfigurować co najmniej jedną z następujących opcji w celu skonfigurowania jako metody weryfikacji zabezpieczeń. Jeśli dostępnych jest wiele opcji, zdecydowanie zalecamy użycie więcej niż jednej metody weryfikacji zabezpieczeń, na wypadek gdyby jedna z metod stała się niedostępna.

    - **Aplikacja uwierzytelniania.** Wybierz użycie aplikacji Microsoft Authenticator lub innej aplikacji uwierzytelniania jako metody weryfikacji zabezpieczeń. Aby uzyskać więcej informacji na temat konfigurowania aplikacji, zobacz [Konfigurowanie aplikacji Microsoft Authenticator jako metody weryfikacji](security-info-setup-auth-app.md).

    - **Wiadomości SMS.** Wybierz wysyłanie wiadomości tekstowych do urządzenia przenośnego. Aby uzyskać więcej informacji o konfigurowaniu wiadomości SMS, zobacz [Konfigurowanie wiadomości SMS jako metody weryfikacji](security-info-setup-text-msg.md).

    - **Rozmowy telefoniczne.** Wybierz, aby nawiązać połączenie telefoniczne z zarejestrowanym numerem telefonu. Aby uzyskać więcej informacji na temat konfigurowania połączeń telefonicznych, zobacz [Konfigurowanie numeru telefonu jako metody weryfikacji](security-info-setup-phone-number.md).

    - **Klucz zabezpieczeń.** Wybierz użycie klucza zabezpieczeń zgodnego z firmą Microsoft. Aby uzyskać więcej informacji, zobacz [Konfigurowanie klucza zabezpieczeń jako metody weryfikacji](security-info-setup-security-key.md).

    - **Adres e-mail.** Wybierz użycie alternatywnego adresu e-mail, który może być używany bez konieczności zapomnianego lub brakującego hasła. Działa to tylko w przypadku resetowania hasła, a nie jako metody weryfikacji zabezpieczeń. Aby uzyskać więcej informacji o konfigurowaniu adresu e-mail, zobacz [Konfigurowanie adresu e-mail jako metody weryfikacji](security-info-setup-email.md).

    - **Pytania zabezpieczające.** Wybierz, aby skonfigurować i odpowiedzieć wstępnie zdefiniowane pytania zabezpieczające skonfigurowane przez administratora. Działa to tylko w przypadku resetowania hasła, a nie jako metody weryfikacji zabezpieczeń. Aby uzyskać więcej informacji na temat pytań zabezpieczających, zobacz [Konfigurowanie pytań zabezpieczających jako metody weryfikacji](security-info-setup-questions.md).

3. Po wybraniu i skonfigurowaniu metod wybierz pozycję **Zakończ** , aby ukończyć proces.

    > [!Note]
    > Informacje dodane do numeru telefonu lub adresu e-mail nie są udostępniane w katalogu globalnym Twojej organizacji. Jedyne osoby, które mogą zobaczyć te informacje, to ty i administrator. Odpowiedzi na pytania zabezpieczające są widoczne tylko dla użytkownika.

## <a name="common-problems-and-their-solutions"></a>Typowe problemy i ich rozwiązania

 Poniżej przedstawiono kilka typowych przypadków błędów i ich rozwiązań:

| Komunikat o błędzie |  Możliwe rozwiązanie |
| --- | --- | --- |
| Skontaktuj się z administratorem.<br>Wykryliśmy, że hasło konta użytkownika nie jest zarządzane przez firmę Microsoft. W związku z tym nie można automatycznie zresetować hasła.<br>Skontaktuj się z pracownikami IT w celu uzyskania dalszej pomocy.| Jeśli ten komunikat o błędzie zostanie wyświetlony po wpisaniu identyfikatora użytkownika, oznacza to, że Twoja organizacja wewnętrznie zarządza hasłem i nie chce resetowania hasła przy użyciu linku **nie można uzyskać dostępu do konta** . Aby zresetować hasło w tej sytuacji, należy skontaktować się z działem pomocy technicznej organizacji lub administratorem w celu uzyskania pomocy. |
| Twoje konto nie ma włączonej obsługi resetowania hasła.<br>Niestety, pracownicy IT nie skonfigurowali Twojego konta do użycia w ramach tej usługi.<br>Jeśli chcesz, możemy skontaktować się z administratorem w organizacji w celu zresetowania hasła. | Jeśli ten komunikat o błędzie zostanie wyświetlony po wpisaniu identyfikatora użytkownika, oznacza to, że Twoja organizacja nie włączyła funkcji resetowania hasła lub nie będzie można jej używać. Aby zresetować hasło w tej sytuacji, należy wybrać łącze **skontaktuj się z administratorem** . Po kliknięciu linku zostanie wysłana wiadomość e-mail do działu pomocy technicznej lub administratora Twojej organizacji, co oznacza, że użytkownik chce zresetować hasło. |
| Nie można zweryfikować Twojego konta.<br>Jeśli chcesz, możemy skontaktować się z administratorem w organizacji w celu zresetowania hasła. | Jeśli ten komunikat o błędzie zostanie wyświetlony po wpisaniu identyfikatora użytkownika, oznacza to, że Twoja organizacja włączyła Resetowanie hasła i można z niej korzystać, ale nie została ona zarejestrowana w usłudze. W takiej sytuacji należy skontaktować się z działem pomocy technicznej lub administratorem organizacji w celu zresetowania hasła. Aby uzyskać informacje na temat rejestrowania się w celu zresetowania hasła po ponownym uruchomieniu urządzenia, zapoznaj się z procesem opisanym w tym artykule. |

## <a name="next-steps"></a>Następne kroki

- [Zmienianie hasła przy użyciu funkcji samoobsługowego resetowania hasła](active-directory-passwords-update-your-own-password.md)

- [Strona z informacjami o zabezpieczeniach](https://mysignins.microsoft.com/security-info)

- [Portal resetowania hasła](https://passwordreset.microsoftonline.com/)

- [Kiedy nie możesz zalogować się na swoje konto Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)