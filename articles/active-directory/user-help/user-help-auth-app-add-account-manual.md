---
title: Ręcznie Dodaj konto do aplikacji Azure Active Directory | Microsoft Docs
description: Jak ręcznie dodać konta do aplikacji Microsoft Authenticator na potrzeby weryfikacji dwuskładnikowej.
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
ms.openlocfilehash: 3838234f9f65fcf91f8de33e3d7196aa13f2b903
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "83741731"
---
# <a name="manually-add-an-account-to-the-app"></a>Ręczne dodawanie konta do aplikacji

Jeśli aparat fotograficzny nie może przechwycić kodu QR, możesz ręcznie dodać informacje o koncie do aplikacji Microsoft Authenticator na potrzeby weryfikacji dwuskładnikowej. Działa to w przypadku kont służbowych i kont innych niż Microsoft.

W przypadku kodów udostępnianych dla Twoich kont nie jest rozróżniana wielkość liter i nie są wymagane spacje po dodaniu do aplikacji Microsoft Authenticator.

>[!Important]
>Aby można było dodać konto, należy pobrać i zainstalować aplikację Microsoft Authenticator. Jeśli jeszcze tego nie zrobiono, wykonaj kroki opisane w artykule [pobieranie i instalowanie aplikacji](user-help-auth-app-download-install.md) .

## <a name="add-your-work-or-school-account"></a>Dodawanie konta służbowego

1. Zanotuj **kod** i informacje o **adresie URL** na komputerze, na stronie **Konfigurowanie aplikacji mobilnej** . Pozostaw Tę stronę otwartą, aby zobaczyć kod i adres URL.

    ![Ekran, który zawiera kod QR](./media/user-help-auth-app-add-account-manual/auth-app-barcode.png)

2. Otwórz aplikację Microsoft Authenticator, wybierz pozycję **Dodaj konto** na stronie ikona **Dostosowywanie i kontrola** w prawym górnym rogu, a następnie wybierz pozycję **konto służbowe**.

3. Wybierz **lub wprowadź kod ręcznie**.

    ![Ekran służący do skanowania kodu QR](./media/user-help-auth-app-add-account-manual/auth-app-manual-code.png)

4. Wprowadź **kod** i **adres URL** z kroku 1, a następnie wybierz pozycję **Zakończ**.

    ![Ekran służący do wprowadzania kodu i adresu URL](./media/user-help-auth-app-add-account-manual/auth-app-code-url.png)

    Na ekranie **konta** aplikacji jest wyświetlana nazwa konta i sześciocyfrowy kod weryfikacyjny. W celu zapewnienia dodatkowych zabezpieczeń kod weryfikacyjny zmienia się co 30 sekund, uniemożliwiając komuś wielokrotne użycie kodu.

## <a name="add-your-google-account"></a>Dodawanie konta Google

1. Na komputerze wybierz pozycję **nie można skanować jej** z poziomu strony **Konfigurowanie uwierzytelniania** przy użyciu kodu QR.

    Na stronie **nie można skanować kodu kreskowego** jest wyświetlany kod tajny. Pozostaw Tę stronę otwartą, aby zobaczyć kod tajny.

2. Otwórz aplikację Microsoft Authenticator, wybierz pozycję **Dodaj konto** na stronie **Dostosowywanie i sterowanie** w prawym górnym rogu, wybierz pozycję **inne konto (Google, Facebook itp.)**, a następnie wybierz **lub wprowadź kod ręcznie**.

3. Wprowadź **nazwę konta** (na przykład Google) i wpisz **klucz tajny** z kroku 1, a następnie wybierz pozycję **Zakończ**.

4. Na stronie **Konfigurowanie uwierzytelniania** na komputerze wpisz sześciocyfrowy kod weryfikacyjny podany w aplikacji dla konta Google, a następnie wybierz pozycję **Weryfikuj**.

    Na ekranie **konta** aplikacji jest wyświetlana nazwa konta i sześciocyfrowy kod weryfikacyjny. W celu zapewnienia dodatkowych zabezpieczeń kod weryfikacyjny zmienia się co 30 sekund, uniemożliwiając komuś wielokrotne użycie kodu.

    >[!NOTE]
    >Aby uzyskać więcej informacji na temat weryfikacji dwuskładnikowej i konta Google, zobacz temat [Włączanie weryfikacji 2-etapowej](https://support.google.com/accounts/answer/185839) i [Dowiedz się więcej o weryfikacji dwuetapowej](https://www.google.com/landing/2step/help.html).

## <a name="add-your-facebook-account"></a>Dodawanie konta w serwisie Facebook

1. Na stronie **Konfigurowanie za pośrednictwem uwierzytelniania innej firmy** , która obejmuje kod QR, oraz kod zapisany na potrzeby wpisu do aplikacji. Pozostaw Tę stronę otwartą, aby wyświetlić kod.

2. Otwórz aplikację Microsoft Authenticator, wybierz pozycję **Dodaj konto** na stronie **Dostosowywanie i sterowanie** w prawym górnym rogu, wybierz pozycję **inne konto (Google, Facebook itp.)**, a następnie wybierz **lub wprowadź kod ręcznie**.

3. Wprowadź **nazwę konta** (na przykład Facebook) i wpisz **klucz tajny** z kroku 1, a następnie wybierz pozycję **Zakończ**.

4. Na stronie **wystawca uwierzytelnienia** na komputerze wpisz sześciocyfrowy kod weryfikacyjny podany w aplikacji dla konta w serwisie Facebook, a następnie wybierz pozycję **Weryfikuj**.

    Na ekranie **konta** aplikacji jest wyświetlana nazwa konta i sześciocyfrowy kod weryfikacyjny. W celu zapewnienia dodatkowych zabezpieczeń kod weryfikacyjny zmienia się co 30 sekund, uniemożliwiając komuś wielokrotne użycie kodu.

    >[!NOTE]
    >Aby uzyskać więcej informacji na temat weryfikacji dwuskładnikowej i konta w serwisie Facebook, zobacz [co to jest uwierzytelnianie dwuskładnikowe i jak to działa?](https://www.facebook.com/help/148233965247823).

## <a name="add-your-amazon-account"></a>Dodawanie konta Amazon

Konto Amazon można dodać, włączając weryfikację dwuskładnikową, a następnie dodając konto do aplikacji.

1. Na komputerze wybierz pozycję **nie można skanować kodu kreskowego** z poziomu strony **Wybieranie sposobu otrzymywania kodów** z kodem QR.

    **Nie można zeskanować komunikatu z kodem kreskowym** z kodem tajnym. Pozostaw ten komunikat otwarty, aby zobaczyć kod tajny.

2. Otwórz aplikację Microsoft Authenticator, wybierz pozycję **Dodaj konto** na stronie **Dostosowywanie i sterowanie** w prawym górnym rogu, wybierz pozycję **inne konto (Google, Facebook itp.)**, a następnie wybierz **lub wprowadź kod ręcznie**.

3. Wprowadź **nazwę konta** (na przykład Amazon) i wpisz **klucz tajny** z kroku 1, a następnie wybierz pozycję **Zakończ**.

4. Ukończ resztę procesu rejestracji, łącznie z dodaniem metody weryfikacji kopii zapasowej, takiej jak wiadomość SMS, a następnie wybierz pozycję **Wyślij kod**.

5. Na stronie **Dodaj metodę weryfikacji kopii zapasowej** na komputerze wpisz sześciocyfrowy kod weryfikacyjny dostarczony przez metodę weryfikacji kopii zapasowej dla konta Amazon, a następnie wybierz pozycję **Weryfikuj kod i Kontynuuj**.

6. Na prawie gotowe strony Zdecyduj, czy komputer ma być zaufanym urządzeniem, a następnie wybierz **go. Włącz weryfikację Two-Step**.

    Zostanie wyświetlona strona **Zaawansowane ustawienia zabezpieczeń** zawierająca zaktualizowane dane weryfikacji dwuskładnikowej.

    >[!NOTE]
    >Aby uzyskać więcej informacji na temat weryfikacji dwuskładnikowej i konta Amazon, zobacz [Informacje o weryfikacji Two-Step](https://www.amazon.com/gp/help/customer/display.html?nodeId=201596330) i [zalogowaniu się przy użyciu weryfikacji Two-Step](https://www.amazon.com/gp/help/customer/display.html?nodeId=201962440).    

## <a name="next-steps"></a>Następne kroki

- Po dodaniu kont do aplikacji możesz zalogować się przy użyciu aplikacji Microsoft Authenticator na urządzeniu. Aby uzyskać więcej informacji, zobacz [Logowanie przy użyciu aplikacji](user-help-auth-app-sign-in.md).

- Jeśli masz problemy z uzyskaniem kodu weryfikacyjnego dla konto Microsoft osobistych, zobacz sekcję **Rozwiązywanie problemów z kodem weryfikacyjnym** w artykule [konto Microsoft informacje zabezpieczające & kody weryfikacyjne](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes) .

- W przypadku urządzeń z systemem iOS można również utworzyć kopię zapasową poświadczeń konta i powiązanych ustawień aplikacji, takich jak kolejność kont, w chmurze. Aby uzyskać więcej informacji, zobacz [Tworzenie kopii zapasowych i odzyskiwanie danych za pomocą aplikacji Microsoft Authenticator](user-help-auth-app-backup-recovery.md).
