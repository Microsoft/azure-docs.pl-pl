---
title: Dodawanie konto Microsoft osobistych do aplikacji Microsoft Authenticator — Azure AD
description: Dodaj osobiste konta Microsoft, takie jak Outlook.com lub Xbox LIVE do aplikacji Microsoft Authenticator, aby zweryfikować swoją tożsamość przy użyciu weryfikacji dwuskładnikowej.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 04/08/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: ff83a9a4e4bfd4c27dd3f8d3f212e489c3772eb4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "83741628"
---
# <a name="add-personal-microsoft-accounts-to-the-microsoft-authenticator-app"></a>Dodawanie osobistych kont Microsoft do aplikacji Microsoft Authenticator

Dodaj osobiste konta Microsoft, takie jak Outlook.com i konta usługi Xbox LIVE, do aplikacji Microsoft Authenticator zarówno dla standardowego procesu weryfikacji dwuskładnikowej, jak i logowania jednokrotnego przy użyciu hasła.

- **Standardowa metoda weryfikacji dwuskładnikowej.** Wpisz nazwę użytkownika i hasło do urządzenia, na którym się logujesz, a następnie wybierz, czy aplikacja Microsoft Authenticator będzie wysyłać powiadomienie, czy wolisz skopiować skojarzony kod weryfikacyjny z ekranu **konta** aplikacji Microsoft Authenticator.

- **Metoda logowania bezhasłem.** Wpisz nazwę użytkownika na urządzeniu, na którym logujesz się przy użyciu osobistego konto Microsoft, a następnie użyj urządzenia przenośnego, aby sprawdzić, czy używasz odcisku palca, kroju lub numeru PIN. W przypadku tej metody nie musisz wprowadzać hasła.

>[!Important]
>Aby można było dodać konto, należy pobrać i zainstalować aplikację Microsoft Authenticator. Jeśli jeszcze tego nie zrobiono, wykonaj kroki opisane w artykule [pobieranie i instalowanie aplikacji](user-help-auth-app-download-install.md) .

Możesz dodać własne konto Microsoft, najpierw włączając weryfikację dwuetapową, a następnie dodając konto do aplikacji. Nie musisz włączać weryfikacji dwuskładnikowej, aby korzystać tylko z logowania jednokrotnego dla Twojego konta, ale zdecydowanie zalecamy włączenie weryfikacji dwuskładnikowej w celu zapewnienia dodatkowych zabezpieczeń konta.

## <a name="turn-on-two-factor-verification"></a>Włącz weryfikację dwuskładnikową

1. Na komputerze przejdź do strony [podstawy zabezpieczeń](https://account.microsoft.com/security) i zaloguj się przy użyciu konto Microsoft osobistych. Na przykład alain@outlook.com.

2. W dolnej części strony **podstawowe informacje o zabezpieczeniach** wybierz łącze **więcej opcji zabezpieczeń** .

    ![Strona podstawy zabezpieczeń z wyróżnionym linkiem "więcej opcji zabezpieczeń"](./media/user-help-auth-app-add-personal-ms-account/more-security-options-link.png)

3. Przejdź do sekcji **weryfikacja dwuetapowa** i **Wybierz Włączenie tej funkcji.** Możesz również ją wyłączyć, jeśli nie chcesz już używać jej z kontem osobistym.

## <a name="add-your-microsoft-account-to-the-app"></a>Dodawanie konto Microsoft do aplikacji

1. Otwórz aplikację Microsoft Authenticator na urządzeniu przenośnym.

1. W systemie Android wybierz pozycję **Dodaj konto** z ikony **Dostosowywanie i kontrola** w prawym górnym rogu.

    ![Strony wybierania konta systemu Android](media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon.png)

    W systemie iOS wybierz ikonę znaku plus w prawym górnym rogu.

    ![wersja systemu iOS środowiska wyboru konta](media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon-ios.png)

1. Na stronie **Dodawanie konta** wybierz pozycję **konto osobiste**.

1. Wybierz **Zaloguj się przy użyciu konta Microsoft** , aby dodać Twoje konto. Kod QR może być używany, jeśli jest dostępny, ale zawsze możesz dodać konto, logując się przy użyciu nazwy użytkownika i hasła.

    ![Wybierz konto Microsoft lub Zeskanuj kod QR, jeśli jest dostępny](media/user-help-auth-app-add-personal-ms-account/add-account-android.png)

1. Zaloguj się do swojego konta osobistego przy użyciu odpowiedniego adresu e-mail (np alain@outlook.com .), a następnie wybierz przycisk **dalej**.

    >[!Note]
    >Jeśli nie masz konto Microsoft osobistych, możesz go [utworzyć](https://account.microsoft.com/account/Account?refd=www.bing.com&ru=https%3A%2F%2Faccount.microsoft.com%2F%3Frefd%3Dwww.bing.com&destrt=home-index).

1. Wprowadź hasło, a następnie wybierz pozycję **Zaloguj się**. Twoje konto osobiste zostanie dodane do aplikacji Microsoft Authenticator.

## <a name="next-steps"></a>Następne kroki

- Po dodaniu kont do aplikacji możesz zalogować się przy użyciu aplikacji uwierzytelniania na urządzeniu. Aby uzyskać więcej informacji, zobacz [Logowanie przy użyciu aplikacji](user-help-auth-app-sign-in.md).

- Jeśli masz problemy z uzyskaniem kodu weryfikacyjnego dla konto Microsoft osobistych, zobacz sekcję **Rozwiązywanie problemów z kodem weryfikacyjnym** w artykule [konto Microsoft informacje zabezpieczające & kody weryfikacyjne](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes) .

- W przypadku urządzeń z systemem iOS można również utworzyć kopię zapasową poświadczeń konta i powiązanych ustawień aplikacji, takich jak kolejność kont, w chmurze. Aby uzyskać więcej informacji, zobacz [Tworzenie kopii zapasowych i odzyskiwanie danych za pomocą aplikacji Microsoft Authenticator](user-help-auth-app-backup-recovery.md).
