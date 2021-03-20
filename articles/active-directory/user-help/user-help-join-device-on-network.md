---
title: Dołączanie urządzenia służbowego do sieci organizacji — AD
description: Dowiedz się, jak dołączać urządzenie służbowe do sieci organizacji.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 08/03/2018
ms.author: curtand
ms.reviewer: jairoc
ms.openlocfilehash: efb3ee24add847baf8264eccdf71278be5ee9496
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91536974"
---
# <a name="join-your-work-device-to-your-organizations-network"></a>Przyłącz urządzenie służbowe do sieci organizacji
Dołącz do sieci swojej organizacji swoje urządzenie z systemem Windows 10, aby uzyskać dostęp do zasobów, które mogą być ograniczone.

## <a name="what-happens-when-you-join-your-device"></a>Co się stanie po dołączeniu urządzenia
Po dołączeniu urządzenia z systemem Windows 10 do sieci organizacji zostaną wykonane następujące akcje:

- System Windows rejestruje urządzenie w sieci organizacji, umożliwiając dostęp do zasobów przy użyciu konta osobistego. Gdy urządzenie zostanie zarejestrowane, system Windows przyłączy urządzenie do sieci, dzięki czemu możesz zalogować się i uzyskać dostęp do zasobów z ograniczeniami, korzystając z nazwy użytkownika i hasła swojej organizacji.

- Opcjonalnie w zależności od opcji dostępnych w organizacji może zostać wyświetlony monit o skonfigurowanie weryfikacji dwuetapowej za pośrednictwem [Multi-Factor Authentication](multi-factor-authentication-end-user-first-time.md) lub [informacji zabezpieczających](./security-info-setup-signin.md).

- Opcjonalnie w zależności od opcji dostępnych w organizacji może być automatycznie rejestrowane w usłudze zarządzania urządzeniami przenośnymi, takie jak Microsoft Intune. Aby uzyskać więcej informacji na temat rejestrowania w Microsoft Intune, zobacz [Rejestrowanie urządzenia w usłudze Intune](/intune-user-help/enroll-your-device-in-intune-all).

- Przejdziesz przez proces logowania, korzystając z automatycznego logowania przy użyciu konta organizacyjnego.

## <a name="to-join-a-brand-new-windows-10-device"></a>Aby przyłączyć nowe urządzenie z systemem Windows 10 do marki
Jeśli urządzenie jest marki nowe i nie zostało jeszcze skonfigurowane, możesz przejść przez proces Windows out of Box Experience (OOBE), aby dołączyć urządzenie do sieci.

1. Uruchom nowe urządzenie i Rozpocznij proces OOBE.

2. Na ekranie **Zaloguj się przy użyciu konta Microsoft** wpisz swój służbowy adres e-mail.

    ![Ekran logowania z adresem e-mail](./media/user-help-join-device-on-network/join-device-oobe-signin.png)

3. Na ekranie **Wprowadź hasło** wpisz hasło.

    ![Wprowadzanie ekranu hasła](./media/user-help-join-device-on-network/join-device-oobe-password.png)

4. Na urządzeniu przenośnym Zatwierdź urządzenie, aby umożliwić mu dostęp do Twojego konta. 

    ![Zrzut ekranu przedstawiający ekran powiadomienia "wprowadź hasło".](./media/user-help-join-device-on-network/join-device-oobe-mobile.png)

5. Ukończ proces OOBE, w tym ustawienia prywatności i Konfigurowanie systemu Windows Hello (jeśli to konieczne).

    Urządzenie jest teraz przyłączone do sieci organizacji.

## <a name="to-make-sure-youre-joined-new-device"></a>Aby upewnić się, że nastąpiło przyłączenie (nowe urządzenie)
Możesz upewnić się, że są one przyłączone do Twoich ustawień.

1. Otwórz pozycję **Ustawienia**, a następnie wybierz pozycję **konta**.

    ![Konta na ekranie Ustawienia](./media/user-help-join-device-on-network/join-device-settings-accounts.png)

2. Wybierz pozycję **dostęp do** zasobów służbowych i upewnij się, że widzisz tekst podobny do tego, **połączony z *\<your_organization>* usługą Azure AD**.

    ![Zrzut ekranu przedstawiający okno "dostęp do pracy lub nauki" z wybranym kontem "Połączono do (Twoja organizacja") usługi Azure AD.](./media/user-help-join-device-on-network/join-device-oobe-verify.png)


## <a name="to-join-an-already-configured-windows-10-device"></a>Aby dołączyć już skonfigurowane urządzenie z systemem Windows 10
Jeśli urządzenie zostało już przez pewien czas skonfigurowane, możesz wykonać następujące kroki, aby dołączyć urządzenie do sieci.

1. Otwórz pozycję **Ustawienia**, a następnie wybierz pozycję **konta**.

2. Wybierz pozycję **dostęp do** zasobów służbowych, a następnie wybierz pozycję **Połącz**.

    ![Dostęp do linków służbowych i połączeń](./media/user-help-join-device-on-network/join-device-access-work-school-connect.png)

3. Na ekranie **Konfigurowanie konta służbowego** wybierz pozycję **Dołącz do tego urządzenia, aby Azure Active Directory**.

    ![Konfigurowanie ekranu konta służbowego](./media/user-help-join-device-on-network/join-device-setup-join-aad.png)

4. Na ekranie **Zaloguj się,** wpisz swój adres e-mail (na przykład alain@contoso.com ), a następnie wybierz przycisk **dalej**.

    ![Zaloguj się na ekranie](./media/user-help-join-device-on-network/join-device-setup-get-signed-in.png)

5. Na ekranie **wprowadzanie hasła** wpisz hasło, a następnie wybierz pozycję **Zaloguj się**.

    ![Wprowadź hasło](./media/user-help-join-device-on-network/join-device-setup-password.png)

6. Na urządzeniu przenośnym Zatwierdź urządzenie, aby umożliwić mu dostęp do Twojego konta. 

    ![Ekran powiadomień mobilnych](./media/user-help-join-device-on-network/join-device-setup-mobile.png)

7. Na ekranie  **upewnij się, że jest to Twoja organizacja** , zapoznaj się z informacjami, aby upewnić się, że są odpowiednie, a następnie wybierz pozycję **Dołącz**.

    ![Upewnij się, że jest ekran weryfikacji organizacji](./media/user-help-join-device-on-network/join-device-setup-confirm.png)

8. Na ekranie **wszystko** gotowe kliknij pozycję **gotowe**.

    ![Wszystko jest ustawione na ekranie](./media/user-help-join-device-on-network/join-device-setup-finish.png)

## <a name="to-make-sure-youre-joined"></a>Aby upewnić się, że jesteś przyłączony
Możesz upewnić się, że są one przyłączone do Twoich ustawień.

1. Otwórz pozycję **Ustawienia**, a następnie wybierz pozycję **konta**.

    ![Konta na ekranie Ustawienia](./media/user-help-join-device-on-network/join-device-settings-accounts.png)

2. Wybierz pozycję **dostęp do** zasobów służbowych i upewnij się, że widzisz tekst podobny do tego, **połączony z *\<your_organization>* usługą Azure AD**.

    ![Uzyskaj dostęp do ekranu służbowego z podłączonym kontem contoso](./media/user-help-join-device-on-network/join-device-setup-verify.png)

## <a name="next-steps"></a>Następne kroki
Po dołączeniu urządzenia do sieci organizacji powinno być możliwe uzyskanie dostępu do wszystkich zasobów przy użyciu informacji o koncie służbowym.

- Jeśli Twoja organizacja chce zarejestrować urządzenie osobiste, takie jak telefon, zobacz temat [Rejestrowanie urządzenia osobistego w sieci organizacji](user-help-register-device-on-network.md).

- Jeśli Twoja organizacja jest zarządzana przy użyciu Microsoft Intune i masz pytania dotyczące rejestracji, logowania lub dowolnego problemu związanego z usługą Intune, zobacz [zawartość pomocy użytkownika usługi Intune](/intune-user-help/use-managed-devices-to-get-work-done).