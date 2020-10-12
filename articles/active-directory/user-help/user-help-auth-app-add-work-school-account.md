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
ms.date: 01/24/2019
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: e003c45aa1e7d75b709b7fbf99532fb1302fcbb9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88797653"
---
# <a name="add-your-work-or-school-account-to-the-microsoft-authenticator-app"></a>Dodawanie konta służbowego do aplikacji Microsoft Authenticator

Jeśli Twoja organizacja korzysta z weryfikacji dwuetapowej, możesz skonfigurować konto służbowe tak, aby korzystało z aplikacji Microsoft Authenticator jako jednej z metod weryfikacji.

>[!Important]
>Aby można było dodać konto, należy pobrać i zainstalować aplikację Microsoft Authenticator. Jeśli jeszcze tego nie zrobiono, wykonaj kroki opisane w artykule [pobieranie i instalowanie aplikacji](user-help-auth-app-download-install.md) .

## <a name="add-your-work-or-school-account"></a>Dodawanie konta służbowego

1. Na komputerze przejdź do strony [dodatkowa Weryfikacja zabezpieczeń](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1) .

    >[!Note]
    >Jeśli nie widzisz strony **dodatkowej weryfikacji zabezpieczeń** , istnieje możliwość, że administrator włączył środowisko informacje zabezpieczające (wersja zapoznawcza). W takim przypadku należy postępować zgodnie z instrukcjami podanymi w sekcji [Konfigurowanie informacji zabezpieczających do korzystania z aplikacji uwierzytelniania](security-info-setup-auth-app.md) . Jeśli tak się nie dzieje, musisz skontaktować się z działem pomocy technicznej Twojej organizacji w celu uzyskania pomocy. Więcej informacji o zabezpieczeniach znajduje się w temacie Informacje o zabezpieczeniach [(wersja zapoznawcza) — Omówienie](./security-info-setup-signin.md).

2. Zaznacz pole wyboru obok pozycji **aplikacja uwierzytelniania**, a następnie wybierz pozycję **Konfiguruj**.

    Zostanie wyświetlona strona **Konfigurowanie aplikacji mobilnej** .

    ![Ekran, który zawiera kod QR](./media/user-help-auth-app-download-install/auth-app-barcode.png)

3. Otwórz aplikację Microsoft Authenticator, wybierz pozycję **Dodaj konto** na stronie ikona **Dostosowywanie i kontrola** w prawym górnym rogu, a następnie wybierz pozycję **konto służbowe**.

    >[!Note]
    >Jeśli konfigurujesz aplikację Microsoft Authenticator po raz pierwszy, może zostać wyświetlony monit z pytaniem, czy zezwolić aplikacji na dostęp do aparatu (iOS), czy zezwolić aplikacji na wykonywanie zdjęć i nagrywanie wideo (Android). Musisz wybrać opcję **Zezwól** , aby aplikacja Authenticator mogła uzyskać dostęp do Twojego aparatu, aby w następnym kroku uzyskać zdjęcie kodu QR. Jeśli nie zezwolisz na korzystanie z aparatu, nadal możesz skonfigurować aplikację uwierzytelniającej, ale musisz ręcznie dodać informacje o kodzie. Aby dowiedzieć się, jak ręcznie dodać kod, zobacz [Ręczne dodawanie konta do aplikacji](user-help-auth-app-add-account-manual.md).

4. Za pomocą aparatu urządzenia Przeskanuj kod QR na ekranie **Konfigurowanie aplikacji mobilnej** na komputerze, a następnie wybierz pozycję **gotowe**.

    >[!Note]
    >Jeśli aparat fotograficzny nie może przechwycić kodu QR, możesz ręcznie dodać informacje o koncie do aplikacji Microsoft Authenticator na potrzeby weryfikacji dwuskładnikowej. Aby uzyskać więcej informacji i dowiedzieć się, jak to zrobić, zobacz [Ręczne dodawanie konta](user-help-auth-app-add-account-manual.md).

5. Przejrzyj ekran **accounts (konta** ) aplikacji na urządzeniu, aby upewnić się, że Twoje konto jest prawidłowe i że istnieje skojarzony sześciocyfrowy kod weryfikacyjny. W celu zapewnienia dodatkowych zabezpieczeń kod weryfikacyjny zmienia się co 30 sekund, uniemożliwiając komuś wielokrotne użycie kodu.

    ![Ekran konta](./media/user-help-auth-app-download-install/auth-app-accounts.png)

## <a name="next-steps"></a>Następne kroki

- Po dodaniu kont do aplikacji możesz zalogować się przy użyciu aplikacji uwierzytelniania na urządzeniu. Aby uzyskać więcej informacji, zobacz [Logowanie przy użyciu aplikacji](user-help-auth-app-sign-in.md).

- W przypadku urządzeń z systemem iOS można również utworzyć kopię zapasową poświadczeń konta i powiązanych ustawień aplikacji, takich jak kolejność kont, w chmurze. Aby uzyskać więcej informacji, zobacz [Tworzenie kopii zapasowych i odzyskiwanie danych za pomocą aplikacji Microsoft Authenticator](user-help-auth-app-backup-recovery.md).