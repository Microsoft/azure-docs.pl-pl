---
title: Tworzenie kopii zapasowych i odzyskiwanie kont przy użyciu aplikacji Microsoft Authenticator — Azure AD
description: Dowiedz się, jak utworzyć kopię zapasową poświadczeń konta kopii zapasowej i odzyskać je za pomocą aplikacji Microsoft Authenticator.
services: active-directory
author: curtand
manager: daveba
ms.subservice: user-help
ms.service: active-directory
ms.workload: identity
ms.topic: end-user-help
ms.date: 06/03/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: e7608196c962a6918a90b91457f856f3e11b04f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91530905"
---
# <a name="back-up-and-recover-account-credentials-using-the-microsoft-authenticator-app"></a>Tworzenie kopii zapasowej i odzyskiwanie poświadczeń konta przy użyciu aplikacji Microsoft Authenticator

**Dotyczy:**

- urządzenia z systemem iOS w wersji 5.7.0 lub nowszej

- Urządzenia z systemem Android w wersji 6.6.0 lub nowszej

Aplikacja Microsoft Authenticator tworzy kopię zapasową poświadczeń konta i powiązanych ustawień aplikacji, takich jak kolejność kont, w chmurze. Po utworzeniu kopii zapasowej można również użyć aplikacji do odzyskania informacji na nowym urządzeniu, co może uniknąć zablokowania lub konieczności ponownego tworzenia kont.

Każda lokalizacja magazynu kopii zapasowych wymaga posiadania jednego osobistego konto Microsoft, natomiast w systemie iOS wymagane jest również konto usługi iCloud. W tej pojedynczej lokalizacji może być przechowywanych wiele kont. Można na przykład korzystać z konta osobistego, konta służbowego i osobistego, niekonto Microsoftowego, takiego jak Facebook, Google i tak dalej.

> [!IMPORTANT]
> Przechowywane są tylko poświadczenia konta użytkownika osobistego i innych firm, w tym nazwa użytkownika i kod weryfikacyjny konta, który jest wymagany w celu potwierdzenia tożsamości. Nie przechowujemy żadnych innych informacji skojarzonych z kontami, w tym wiadomości e-mail i plików. Nie kojarzą również ani nie udostępniamy kont w żaden sposób ani z żadnym innym produktem lub usługą. Ponadto administrator IT nie uzyska żadnych informacji o żadnym z tych kont.

## <a name="back-up-your-account-credentials"></a>Tworzenie kopii zapasowej poświadczeń konta

Aby można było utworzyć kopię zapasową poświadczeń, należy dysponować:

- Osobista [konto Microsoft](https://account.microsoft.com/account) do działania jako konto odzyskiwania.

- **Tylko w przypadku systemu iOS** musisz mieć [konto usługi iCloud](https://www.icloud.com/) dla rzeczywistej lokalizacji magazynu.

### <a name="to-turn-on-cloud-backup-for-ios-devices"></a>Aby włączyć usługę kopia zapasowa w chmurze dla urządzeń z systemem iOS

- Na urządzeniu z systemem iOS wybierz pozycję **Ustawienia**, wybierz pozycję **kopia zapasowa**, a następnie włącz opcję **kopia zapasowa iCloud**.

    Kopie zapasowe poświadczeń konta są tworzone na koncie usługi iCloud.

    ![ekran ustawień systemu iOS, przedstawiający lokalizację ustawień kopii zapasowych w usłudze iCloud](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on.png)

### <a name="to-turn-on-cloud-backup-for-android-devices"></a>Aby włączyć usługę kopia zapasowa w chmurze dla urządzeń z systemem Android

- Na urządzeniu z systemem Android wybierz pozycję **Ustawienia**, wybierz pozycję **kopia zapasowa**, a następnie włącz opcję **kopia zapasowa w chmurze**.

    Kopie zapasowe poświadczeń konta są tworzone na koncie w chmurze.

    ![Ekran ustawień systemu Android, pokazujący lokalizację ustawień kopii zapasowej](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on-android.png)

## <a name="recover-your-account-credentials-on-your-new-device"></a>Odzyskaj poświadczenia konta na nowym urządzeniu

Możesz odzyskać poświadczenia konta z konta w chmurze, ale najpierw musisz się upewnić, że konto, które jest odzyskiwane, nie istnieje w aplikacji Microsoft Authenticator. Na przykład w przypadku odzyskiwania konto Microsoft osobistych należy upewnić się, że nie masz już konto Microsoft osobistych skonfigurowanych w aplikacji uwierzytelniania. To sprawdzenie jest ważne, aby upewnić się, że nie zastąpisz ani nie wymazuje istniejącego konta przez pomyłkę.

### <a name="to-recover-your-information"></a>Aby odzyskać informacje

1. Na urządzeniu przenośnym Otwórz aplikację Microsoft Authenticator i wybierz pozycję **Rozpocznij odzyskiwanie** w dolnej części ekranu.

    ![Aplikacja Microsoft Authenticator, pokazująca, gdzie kliknąć przycisk Rozpocznij odzyskiwanie](./media/user-help-auth-app-backup-recovery/backup-and-recovery-begin-recovery.png)

2. Zaloguj się do konta odzyskiwania przy użyciu tego samego osobistego konto Microsoft użytego podczas procesu tworzenia kopii zapasowej.

    Poświadczenia konta zostaną odzyskane do nowego urządzenia.

Po zakończeniu odzyskiwania możesz zauważyć, że własne kody weryfikacyjne konto Microsoft w aplikacji Microsoft Authenticator są różne dla starych i nowych telefonów. Kody są różne, ponieważ każde urządzenie ma własne unikatowe poświadczenia, ale oba są prawidłowe i pracują podczas logowania przy użyciu skojarzonego telefonu.

## <a name="recover-accounts-requiring-more-verification"></a>Odzyskaj konta wymagające większej weryfikacji

Jeśli używasz powiadomień wypychanych z kontami osobistymi lub służbowymi, otrzymasz alert na ekranie z informacją, że musisz podać dodatkową weryfikację, aby móc odzyskać informacje. Ponieważ powiadomienia wypychane wymagają użycia poświadczeń powiązanych z określonym urządzeniem i nigdy nie są wysyłane przez sieć, przed utworzeniem poświadczeń na urządzeniu należy potwierdzić swoją tożsamość.

W przypadku osobistych kont Microsoft można potwierdzić swoją tożsamość, wprowadzając hasło i alternatywny adres e-mail lub numer telefonu. W przypadku kont służbowych należy zeskanować kod QR przyznany przez dostawcę konta.

### <a name="to-provide-more-verification-for-personal-accounts"></a>Aby zapewnić większą weryfikację kont osobistych

1. Na ekranie **konta** aplikacji Microsoft Authenticator wybierz konto, które chcesz odzyskać, aby otworzyć widok pełny ekran konta.

    :::image type="content" source="media/user-help-auth-app-backup-recovery/backup-and-recovery-arrow.png" alt-text="Zrzut ekranu przedstawiający aplikację Microsoft Authenticator z kafelkami dostępne konta." border="true":::

1. Naciśnij kafelek dla konta, które chcesz odzyskać, a następnie naciśnij opcję, aby zalogować się w celu odzyskania. Wprowadź hasło, a następnie potwierdź swój adres e-mail lub numer telefonu jako dodatkową weryfikację.

    :::image type="content" source="media/user-help-auth-app-backup-recovery/backup-and-recovery-codes.png" alt-text="Zrzut ekranu, który pokazuje Microsoft Authenticator kod hasła jednorazowego." border="true":::

### <a name="to-provide-more-verification-for-work-or-school-accounts"></a>Aby zapewnić większą weryfikację kont służbowych

1. Na ekranie **konta** aplikacji Microsoft Authenticator wybierz konto, które chcesz odzyskać, aby otworzyć widok pełny ekran konta.

    :::image type="content" source="media/user-help-auth-app-backup-recovery/work-or-school-recovery-arrow.png" alt-text="Aplikacja Microsoft Authenticator, pokazująca dostępne kafelki konta" border="true":::

1. W widoku pełny ekran naciśnij opcję, aby zeskanować kod QR w celu pełnego odzyskania.

    :::image type="content" source="media/user-help-auth-app-backup-recovery/work-or-school-recovery-qr-code.png" alt-text="Wystawca uwierzytelnienia przedstawia hasło jednorazowe jako kod weryfikacyjny" border="true":::

>[!NOTE]
>Aby uzyskać więcej informacji na temat kodów QR i sposobu ich uzyskania, zobacz [Rozpoczynanie pracy z aplikacją Microsoft Authenticator](./user-help-auth-app-download-install.md) lub [Konfigurowanie informacji zabezpieczających do korzystania z aplikacji Authenticator](./security-info-setup-auth-app.md)w zależności od tego, czy administrator włączył informacje zabezpieczające.
>
>Jeśli konfigurujesz aplikację Microsoft Authenticator po raz pierwszy, może zostać wyświetlony monit z pytaniem, czy zezwolić aplikacji na dostęp do aparatu (iOS), czy zezwolić aplikacji na wykonywanie zdjęć i nagrywanie wideo (Android). Musisz wybrać opcję **Zezwól** , aby aplikacja Authenticator mogła uzyskać dostęp do Twojego aparatu, aby w następnym kroku uzyskać zdjęcie kodu QR. Jeśli nie zezwolisz na korzystanie z aparatu, nadal możesz skonfigurować aplikację uwierzytelniającej, ale musisz ręcznie dodać informacje o kodzie. Aby dowiedzieć się, jak ręcznie dodać kod, zobacz temat [Ręczne dodawanie konta do aplikacji](user-help-auth-app-add-account-manual.md).

## <a name="troubleshoot-backup-and-recovery-problems"></a>Rozwiązywanie problemów z tworzeniem kopii zapasowej i odzyskiwaniem

Istnieje kilka powodów, dla których kopia zapasowa może być niedostępna

- **Zmiana systemów operacyjnych**: kopia zapasowa jest przechowywana w usłudze iCloud dla systemu iOS i w dostawcy magazynu w chmurze firmy Microsoft dla systemu Android. Oznacza to, że kopia zapasowa jest niedostępna w przypadku przełączania między urządzeniami z systemem Android i iOS. W przypadku wprowadzenia przełącznika należy ręcznie utworzyć ponownie konta w aplikacji Microsoft Authenticator.

- **Problemy z siecią**: Jeśli występują problemy związane z siecią, upewnij się, że masz połączenie z siecią i prawidłowo zarejestrowano się na swoim koncie.

- **Problemy z kontem**: Jeśli występują problemy związane z kontem, upewnij się, że użytkownik jest prawidłowo zalogowany na swoim koncie. W przypadku systemu iOS oznacza to, że użytkownik musi być zalogowany w usłudze iCloud przy użyciu tego samego konta usługi AppleID, co Twój telefon iPhone.

- **Przypadkowe usunięcie**: możliwe, że konto kopii zapasowej zostało usunięte z poprzedniego urządzenia lub w trakcie zarządzania kontem magazynu w chmurze. W takiej sytuacji należy ręcznie utworzyć ponownie konto w ramach aplikacji.

- **Istniejące konta Microsoft Authenticator**: Jeśli już skonfigurowano konta w aplikacji Microsoft Authenticator, aplikacja nie będzie mogła odzyskać kont kopii zapasowej. Uniemożliwianie odzyskiwania pomaga upewnić się, że szczegóły konta nie są zastępowane nieaktualnymi informacjami. W takiej sytuacji należy usunąć wszelkie istniejące informacje o koncie z istniejących kont skonfigurowanych w aplikacji uwierzytelniania, zanim będzie możliwe odzyskanie kopii zapasowej.

- **Kopia zapasowa jest nieaktualna**: Jeśli informacje o kopii zapasowej są nieaktualne, może zostać wyświetlony monit o odświeżenie informacji przez ponowne zalogowanie się do konta odzyskiwania firmy Microsoft. Konto odzyskiwania to osobisty konto Microsoft używany początkowo do przechowywania kopii zapasowej. Jeśli logowanie jest wymagane, zobaczysz czerwoną kropkę w menu lub pasku akcji lub zobaczysz ikonę wykrzyknika z monitem o zalogowanie się, aby zakończyć przywracanie z kopii zapasowej. Po wybraniu odpowiedniej ikony zostanie wyświetlony monit o ponowne zalogowanie się w celu zaktualizowania informacji.

## <a name="next-steps"></a>Następne kroki

Teraz, po wykonaniu kopii zapasowej i odzyskaniu poświadczeń konta na nowym urządzeniu, możesz nadal używać aplikacji Microsoft Authenticator, aby zweryfikować swoją tożsamość. Aby uzyskać więcej informacji, zobacz [Logowanie do kont przy użyciu aplikacji Microsoft Authenticator](user-help-sign-in.md).

## <a name="related-articles"></a>Pokrewne artykuły:

- [Czym jest aplikacja Microsoft Authenticator?](user-help-auth-app-overview.md)

- [Microsoft Authenticator app FAQ (Aplikacja Microsoft Authenticator — często zadawane pytania)](user-help-auth-app-faq.md)

- [Uwierzytelnianie wieloskładnikowe](/azure/multi-factor-authentication/)