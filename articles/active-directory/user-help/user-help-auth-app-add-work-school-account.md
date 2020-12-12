---
title: Dodawanie konta służbowego do aplikacji Microsoft Authenticator — usługa Azure AD
description: Dodaj konto służbowe do aplikacji Microsoft Authenticator, aby zweryfikować swoją tożsamość przy użyciu weryfikacji dwuskładnikowej.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 04c9bc429d9663f7ac36b6ba8f40abf225eb71c6
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359119"
---
# <a name="add-your-work-or-school-account-to-the-microsoft-authenticator-app"></a>Dodawanie konta służbowego do aplikacji Microsoft Authenticator

Jeśli Twoja organizacja korzysta z weryfikacji dwuetapowej, możesz skonfigurować konto służbowe tak, aby korzystało z aplikacji Microsoft Authenticator jako jednej z metod weryfikacji.

>[!Important]
>Aby można było dodać konto, należy pobrać i zainstalować aplikację Microsoft Authenticator. Jeśli jeszcze tego nie zrobiono, wykonaj kroki opisane w artykule [pobieranie i instalowanie aplikacji](user-help-auth-app-download-install.md) .

## <a name="add-your-work-or-school-account"></a>Dodawanie konta służbowego

Konto służbowe możesz dodać do aplikacji Microsoft Authenticator, wykonując jedną z następujących czynności:

- Poświadczenia Zaloguj się za pomocą konta służbowego (wersja zapoznawcza)
- Skanuj kod QR

### <a name="sign-in-with-your-credentials"></a>Logowanie się przy użyciu poświadczeń

>[!Note]
>Ta funkcja jest używana tylko przez użytkowników, których administratorzy włączyli logowanie za pomocą telefonu przy użyciu aplikacji uwierzytelniania.

Aby dodać konto, logując się do konta służbowego przy użyciu poświadczeń:

1. Otwórz aplikację Microsoft Authenticator i wybierz pozycję na **+** przycisk, a następnie naciśnij pozycję **Dodaj konto służbowe**. Wybierz pozycję **Zaloguj się**.

1. Wprowadź swoje poświadczenia konta służbowego. Jeśli masz dostęp do tymczasowego (TAP), możesz go użyć do zalogowania się. W tym momencie można zablokować możliwość przejścia z jednego z następujących warunków:

   - Jeśli nie masz wystarczającej metody uwierzytelniania na koncie w celu uzyskania tokenu silnego uwierzytelniania, nie będzie można przejść do dodawania konta.

   - Jeśli zostanie wyświetlony komunikat `You might be signing in from a location that is restricted by your admin` , użytkownik jest blokowany i musi mieć uprawnienia administratora, aby odblokować [informacje zabezpieczające](https://mysignins.microsoft.com/security-info).

   - Jeśli użytkownik nie zablokował logowania za pomocą telefonu przy użyciu aplikacji uwierzytelniania przez administratora, będzie można przejść przez usługę rejestracji urządzeń w celu skonfigurowania funkcji logowania jednokrotnego i usługi Azure Multi-Factor Authentication (MFA).

1. W tym momencie może zostać wyświetlony monit o przeskanowanie kodu QR dostarczonego przez organizację w celu skonfigurowania lokalnego konta uwierzytelniania wieloskładnikowego w aplikacji. Jest to wymagane tylko wtedy, gdy organizacja używa lokalnego serwera usługi MFA.

1. Na urządzeniu naciśnij konto i sprawdź, czy w widoku pełnego ekranu jest poprawna wartość konta i czy istnieje skojarzony sześciocyfrowy kod weryfikacyjny. W celu zapewnienia dodatkowych zabezpieczeń kod weryfikacyjny zmienia się co 30 sekund, uniemożliwiając komuś wielokrotne użycie kodu.

## <a name="sign-in-with-a-qr-code"></a>Zaloguj się przy użyciu kodu QR

Aby dodać konto przez skanowanie kodu QR, wykonaj następujące czynności:

1. Na komputerze przejdź do strony **dodatkowa Weryfikacja zabezpieczeń** .

   >[!Note]
   >Jeśli nie widzisz strony **dodatkowej weryfikacji zabezpieczeń** , istnieje możliwość, że administrator włączył środowisko informacje zabezpieczające (wersja zapoznawcza). W takim przypadku należy postępować zgodnie z instrukcjami podanymi w sekcji [Konfigurowanie informacji zabezpieczających do korzystania z aplikacji uwierzytelniania](security-info-setup-auth-app.md) . Jeśli tak się nie dzieje, musisz skontaktować się z działem pomocy technicznej Twojej organizacji w celu uzyskania pomocy. Aby uzyskać więcej informacji o zabezpieczeniach, zobacz [Konfigurowanie informacji zabezpieczających za pomocą monitu logowania](security-info-setup-signin.md).

1. Zaznacz pole wyboru obok pozycji aplikacja uwierzytelniania, a następnie wybierz pozycję **Konfiguruj**. Zostanie wyświetlona strona **Konfigurowanie aplikacji mobilnej** .

   ![Ekran, który zawiera kod QR](./media/user-help-auth-app-add-work-school-account/auth-app-barcode.png)

1. Otwórz aplikację Microsoft Authenticator, wybierz ikonę plusa, a ![ następnie wybierz ikonę znaku plus na urządzeniach z systemem iOS lub Android, ](media/user-help-auth-app-add-work-school-account/plus-icon.png) a następnie wybierz pozycję **Dodaj konto**, a następnie wybierz pozycję **konto służbowe,** a następnie **Zeskanuj kod QR**.
   Jeśli nie masz skonfigurowanego konta w aplikacji Authenticator, zobaczysz duży niebieski przycisk informujący o **dodaniu konta**.

Jeśli nie zostanie wyświetlony monit o użycie aparatu do skanowania kodu QR, w ustawieniach telefonu upewnij się, że aplikacja Authenticator ma dostęp do aparatu telefonu.

## <a name="next-steps"></a>Następne kroki

- Po dodaniu kont do aplikacji możesz zalogować się przy użyciu aplikacji uwierzytelniania na urządzeniu. Aby uzyskać więcej informacji, zobacz [Logowanie przy użyciu aplikacji](user-help-auth-app-sign-in.md).

- W przypadku urządzeń z systemem iOS można również utworzyć kopię zapasową poświadczeń konta i powiązanych ustawień aplikacji, takich jak kolejność kont, w chmurze. Aby uzyskać więcej informacji, zobacz [Tworzenie kopii zapasowych i odzyskiwanie danych za pomocą aplikacji Microsoft Authenticator](user-help-auth-app-backup-recovery.md).
