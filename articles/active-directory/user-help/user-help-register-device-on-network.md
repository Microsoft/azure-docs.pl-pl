---
title: Rejestrowanie urządzeń osobistych w sieci organizacji — Azure AD
description: Dowiedz się, jak zarejestrować swoje urządzenie osobiste w sieci organizacji, aby uzyskać dostęp do chronionych zasobów organizacji.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 01/04/2019
ms.author: curtand
ms.reviewer: jairoc
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 68d7b06aa6473a99422d8cfb51f6e3a465933a0f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83747391"
---
# <a name="register-your-personal-device-on-your-organizations-network"></a>Rejestrowanie urządzenia osobistego w sieci organizacji
Zarejestruj swoje urządzenie osobiste (zazwyczaj telefon lub tablet) w sieci organizacji. Po zarejestrowaniu urządzenia będzie możliwe uzyskanie dostępu do zasobów z ograniczeniami organizacji.

>[!Note]
>W tym artykule jest używane urządzenie z systemem Windows w celach demonstracyjnych, ale można również zarejestrować urządzenia z systemem iOS, Android lub macOS.

## <a name="what-happens-when-you-register-your-device"></a>Co się stanie po zarejestrowaniu urządzenia
Podczas rejestrowania urządzenia w sieci organizacji zostaną wykonane następujące akcje:

- System Windows rejestruje urządzenie w sieci organizacji.

- Opcjonalnie w zależności od opcji dostępnych w organizacji może zostać wyświetlony monit o skonfigurowanie weryfikacji dwuetapowej za pośrednictwem [Multi-Factor Authentication](multi-factor-authentication-end-user-first-time.md) lub [informacji zabezpieczających](user-help-security-info-overview.md).

- Opcjonalnie w zależności od opcji dostępnych w organizacji może być automatycznie rejestrowane w usłudze zarządzania urządzeniami przenośnymi, takie jak Microsoft Intune. Aby uzyskać więcej informacji na temat rejestrowania w Microsoft Intune, zobacz [Rejestrowanie urządzenia w usłudze Intune](https://docs.microsoft.com/intune-user-help/enroll-your-device-in-intune-all).

- Przejdziesz przez proces logowania przy użyciu nazwy użytkownika i hasła do konta służbowego.

## <a name="to-register-your-windows-device"></a>Aby zarejestrować urządzenie z systemem Windows

Wykonaj następujące kroki, aby zarejestrować swoje urządzenie osobiste w sieci.

1. Otwórz pozycję **Ustawienia**, a następnie wybierz pozycję **konta**.

    ![Konta na ekranie Ustawienia](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. Wybierz pozycję **dostęp do**zasobów służbowych, a następnie wybierz pozycję **Połącz** z ekranu **dostęp do** zasobów służbowych.

    ![Ekran pracy lub szkoły z wyróżnioną opcją Połącz](./media/user-help-register-device-on-network/register-device-access-work-school-connect.png)

3. Na ekranie **Dodawanie konta** służbowego wpisz adres e-mail swojego konta służbowego, a następnie wybierz przycisk **dalej**. Na przykład alain@contoso.com.

4. Zaloguj się do konta służbowego, a następnie wybierz pozycję **Zaloguj się**.

5. Ukończ pozostałą część procesu rejestracji, łącznie z zatwierdzaniem żądania weryfikacji tożsamości (w przypadku korzystania z weryfikacji dwuetapowej) i konfigurowaniu systemu Windows Hello (jeśli to konieczne).

## <a name="to-verify-that-youre-registered"></a>Aby sprawdzić, czy zarejestrowano
Możesz sprawdzić, czy Twoje ustawienia zostały zarejestrowane.

1. Otwórz pozycję **Ustawienia**, a następnie wybierz pozycję **konta**.

    ![Konta na ekranie Ustawienia](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. Wybierz pozycję **dostęp do**zasobów służbowych i upewnij się, że widzisz konto służbowe.

    ![Uzyskaj dostęp do ekranu służbowego z podłączonym kontem contoso](./media/user-help-register-device-on-network/register-device-setup-verify.png)

## <a name="next-steps"></a>Następne kroki
Po zarejestrowaniu urządzenia osobistego w sieci organizacji powinno być możliwe uzyskanie dostępu do większości zasobów.

- Jeśli Twoja organizacja chce dołączyć do urządzenia służbowego, zobacz sekcję [Przyłącz urządzenie służbowe do sieci organizacji](user-help-join-device-on-network.md).



