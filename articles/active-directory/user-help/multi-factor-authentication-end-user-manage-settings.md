---
title: Zmień metodę weryfikacji dwuskładnikowej i ustawienia — Azure Active Directory
description: Dowiedz się, jak zmienić metodę weryfikacji zabezpieczeń i ustawienia dla konta służbowego na stronie dodatkowej weryfikacji zabezpieczeń.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: richagi
ms.assetid: d3372d9a-9ad1-4609-bdcf-2c4ca9679a3b
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 07/06/2020
ms.author: curtand
ms.openlocfilehash: e0a6c566e8e0dfb77b5899f735020d0f1facf3d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88798384"
---
# <a name="change-your-two-factor-verification-method-and-settings"></a>Zmiana metody i ustawień weryfikacji dwuskładnikowej

Po skonfigurowaniu metod weryfikacji zabezpieczeń dla konta służbowego możesz zaktualizować dowolne powiązane informacje, takie jak:

- Domyślna metoda weryfikacji zabezpieczeń

- Szczegóły metody weryfikacji zabezpieczeń, takie jak numer telefonu

- Konfiguracja aplikacji uwierzytelniania lub usuwanie urządzenia z aplikacji uwierzytelniania

## <a name="using-the-additional-security-verification-page"></a>Korzystanie ze strony dodatkowej weryfikacji zabezpieczeń

Jeśli Twoja organizacja dostarczyła konkretne kroki dotyczące włączania weryfikacji dwuskładnikowej i zarządzania nią, należy najpierw wykonać te instrukcje. W przeciwnym razie możesz przejść do ustawień metody weryfikacji zabezpieczeń na stronie [dodatkowej weryfikacji zabezpieczeń](./multi-factor-authentication-end-user-first-time.md) .

>[!Note]
>Jeśli dane wyświetlane na ekranie nie są zgodne z tym, co zostało omówione w tym artykule, oznacza to, że administrator włączył środowisko **informacje zabezpieczające (wersja zapoznawcza)** lub że Twoja organizacja dostarczyła własny portal niestandardowy. Aby uzyskać więcej informacji na temat nowego środowiska informacji zabezpieczających, zobacz [informacje zabezpieczające (wersja zapoznawcza) — Omówienie](./security-info-setup-signin.md). Aby uzyskać więcej informacji na temat portalu niestandardowego w organizacji, należy skontaktować się z działem pomocy technicznej w organizacji.

### <a name="to-get-to-the-additional-security-verification-page"></a>Aby przejść do strony dodatkowej weryfikacji zabezpieczeń

Możesz skorzystać z tego linku do [strony dodatkowej weryfikacji zabezpieczeń](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1).

![Dodatkowa strona weryfikacji zabezpieczeń z informacjami o dostępnej metodzie weryfikacji zabezpieczeń](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page.png)

Możesz również przejść do strony **dodatkowej weryfikacji zabezpieczeń** , wykonując następujące czynności:

1. Zaloguj się do witryny [https://myapps.microsoft.com](https://myapps.microsoft.com).

1. Wybierz nazwę swojego konta w prawym górnym rogu, a następnie wybierz pozycję **profil**.

1. Wybierz opcję **dodatkowej weryfikacji zabezpieczeń**.  

    ![Link Moje aplikacje do strony dodatkowej weryfikacji zabezpieczeń](./media/multi-factor-authentication-end-user-manage-settings/mfa-myapps-link.png)

>[!Note]
>Aby uzyskać informacje o korzystaniu z sekcji **hasła aplikacji** na stronie **dodatkowej weryfikacji zabezpieczeń** , zobacz [Zarządzanie hasłami aplikacji w celu weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-app-passwords.md). Haseł aplikacji należy używać tylko w przypadku aplikacji, które nie obsługują weryfikacji dwuskładnikowej.

## <a name="change-your-default-security-verification-method"></a>Zmień domyślną metodę weryfikacji zabezpieczeń

Po zalogowaniu się do konta służbowego przy użyciu nazwy użytkownika i hasła zostanie automatycznie wyświetlona wybrana metoda weryfikacji zabezpieczeń. W zależności od wymagań organizacji może to być kod powiadomienia lub weryfikacyjne za pomocą aplikacji uwierzytelniającej, wiadomości tekstowej lub połączenia telefonicznego.

Jeśli zdecydujesz się zmienić domyślną metodę weryfikacji zabezpieczeń, z której korzystasz, możesz to zrobić w tym miejscu.

### <a name="to-change-your-default-security-verification-method"></a>Aby zmienić domyślną metodę weryfikacji zabezpieczeń

1. Na stronie **dodatkowa Weryfikacja zabezpieczeń** wybierz metodę, która ma zostać użyta z listy **opcji co jest preferowane** . Zobaczysz wszystkie opcje, ale możesz wybrać tylko te, które są udostępniane przez organizację.

    - **Powiadom mnie za pomocą aplikacji**: otrzymasz powiadomienie za pomocą aplikacji uwierzytelniającej, że masz monit o weryfikację oczekującą.

    - **Zadzwoń na mój telefon uwierzytelniania**: otrzymasz połączenie telefoniczne na urządzeniu przenośnym, z prośbą o zweryfikowanie informacji.

    - **Kod tekstowy na mój numer telefonu uwierzytelniania**: otrzymasz kod weryfikacyjny jako część wiadomości tekstowej na urządzeniu przenośnym. Musisz wprowadzić ten kod do monitu weryfikacji dla konta służbowego.

    - **Zadzwoń na mój telefon służbowy**: nastąpi połączenie telefoniczne na telefonie służbowym, z prośbą o zweryfikowanie informacji.

    - **Użyj kodu weryfikacyjnego z aplikacji**: użyjesz aplikacji uwierzytelniania do uzyskania kodu weryfikacyjnego, który zostanie wpisany do monitu z konta służbowego.

2. Wybierz pozycję **Zapisz**.

## <a name="add-or-change-your-phone-number"></a>Dodawanie lub zmiana numeru telefonu

Możesz dodać nowe numery telefonów lub zaktualizować istniejące numery na stronie **dodatkowej weryfikacji zabezpieczeń** .

>[!Important]
>Zdecydowanie zalecamy dodanie dodatkowego numeru telefonu, aby zapobiec zablokowaniu konta w przypadku jego zgubienia lub kradzieży, lub jeśli otrzymasz nowy numer telefonu i nie będziesz już mieć oryginalnego, podstawowego numeru telefonu.

### <a name="to-change-your-phone-numbers"></a>Aby zmienić numery telefonów

1. Na stronie **jak chcesz odpowiedzieć?** w sekcji **dodatkowa Weryfikacja zabezpieczeń** Zaktualizuj informacje o numerze telefonu dla **telefonu uwierzytelniania** (podstawowego urządzenia przenośnego) i **telefonu biurowego**.

1. Zaznacz pole obok opcji **numer telefonu uwierzytelniania alternatywnego** , a następnie wpisz dodatkowy numer telefonu, w którym można odbierać połączenia telefoniczne, jeśli nie możesz uzyskać dostępu do urządzenia podstawowego.

1. Wybierz pozycję **Zapisz**.

## <a name="add-a-new-account-to-the-microsoft-authenticator-app"></a>Dodawanie nowego konta do aplikacji Microsoft Authenticator

Konto służbowe można skonfigurować w aplikacji Microsoft Authenticator dla [systemu Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) lub [iOS](https://apps.apple.com/app/microsoft-authenticator/id983156458).

Jeśli konto służbowe zostało już skonfigurowane w aplikacji Microsoft Authenticator, nie musisz tego robić ponownie.

1. Na stronie w **jaki sposób chcesz odpowiedzieć?** w obszarze **dodatkowa Weryfikacja zabezpieczeń** wybierz pozycję **Skonfiguruj aplikację uwierzytelniania**.

    ![Konfigurowanie konta służbowego w aplikacji Microsoft Authenticator](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page-auth-app.png)

1. Postępuj zgodnie z instrukcjami wyświetlanymi na ekranie, w tym za pomocą urządzenia przenośnego, aby skanować kod QR, a następnie wybierz przycisk **dalej**.

    Użytkownik zostanie poproszony o zatwierdzenie powiadomienia za pomocą aplikacji Microsoft Authenticator w celu zweryfikowania informacji.

1. Wybierz pozycję **Zapisz**.

## <a name="delete-your-account-or-device-from-the-microsoft-authenticator-app"></a>Usuwanie konta lub urządzenia z aplikacji Microsoft Authenticator

Konto można usunąć z aplikacji Microsoft Authenticator i usunąć je z konta służbowego. Zwykle usuniesz urządzenie, aby trwale usunąć zgubione lub skradzione urządzenie z Twojego konta, a następnie usuń swoje konto, aby spróbować naprawić niektóre problemy z połączeniem lub rozwiązać zmianę konta, na przykład nową nazwę użytkownika.

### <a name="to-delete-your-device-from-your-work-or-school-account"></a>Aby usunąć urządzenie z konta służbowego

1. Na stronie w **jaki sposób chcesz odpowiedzieć?** w obszarze **dodatkowej weryfikacji zabezpieczeń** wybierz przycisk **Skonfiguruj aplikację uwierzytelniania** .

1. Wybierz pozycję **Zapisz**.

### <a name="to-delete-your-account-from-the-microsoft-authenticator-app"></a>Aby usunąć konto z aplikacji Microsoft Authenticator

W aplikacji Microsoft Authenticator wybierz przycisk **Usuń** obok urządzenia, które chcesz usunąć.

## <a name="turn-on-two-factor-verification-prompts-on-a-trusted-device"></a>Włączanie dwuskładnikowych wierszy weryfikacji na zaufanym urządzeniu

W zależności od ustawień organizacji może pojawić się pole wyboru, które **nie zadawaj ponownie przez X dni** w przypadku przeprowadzania weryfikacji dwuskładnikowej w przeglądarce. Jeśli wybrano tę opcję, aby zatrzymać wyświetlanie dwuskładnikowych wierszy weryfikacyjnych, a następnie utracisz urządzenie lub może dojść do naruszenia zabezpieczeń urządzenia, należy włączyć opcję weryfikacji dwuskładnikowej w celu zapewnienia ochrony Twojego konta. Wszystkie urządzenia muszą być włączone w tym samym czasie. Niestety, nie można ponownie włączyć wyświetlania z powrotem dla konkretnego urządzenia.

### <a name="to-turn-two-factor-verification-prompts-back-on-for-your-devices"></a>Aby włączyć weryfikację dwuskładnikową z powrotem na urządzeniach

Na stronie [ **dodatkowa Weryfikacja zabezpieczeń**](#to-get-to-the-additional-security-verification-page)wybierz pozycję **Przywróć uwierzytelnianie wieloskładnikowe na wcześniej zaufanych urządzeniach**. Przy następnym logowaniu na dowolnym urządzeniu zostanie wyświetlony monit o przeprowadzenie weryfikacji dwuskładnikowej.

## <a name="next-steps"></a>Następne kroki

Po dodaniu lub zaktualizowaniu ustawień weryfikacji dwuskładnikowej możesz zarządzać hasłami aplikacji, zalogować się lub uzyskać pomoc dotyczącą niektórych typowych problemów związanych z weryfikacją dwuskładnikowym.

- [Zarządzaj hasłami aplikacji w celu weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-app-passwords.md) dla wszystkich aplikacji, które nie obsługują weryfikacji dwuskładnikowej.

- [Jak zalogować się przy użyciu weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-signin.md)

- [Rozwiązywanie typowych problemów dotyczących weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-troubleshoot.md)