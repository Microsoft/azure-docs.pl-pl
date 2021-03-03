---
title: Dwukierunkowa wiadomość SMS nie jest już obsługiwana — Azure Active Directory
description: Wyjaśnia, jak włączyć kolejną metodę dla użytkowników, którzy nadal korzystają z dwukierunkowej wiadomości SMS.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/02/2021
ms.author: justinha
author: rhicock
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: d25ed1e46823ec6d820addf3944c96c97fcabcb8
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101689032"
---
# <a name="two-way-sms-unsupported"></a>Nieobsługiwana dwukierunkowa wiadomość SMS

Dwukierunkowa wiadomość SMS dla serwera usługi Azure AD Multi-Factor Authentication (MFA) była pierwotnie przestarzała w 2018 i nie jest już obsługiwana po 24 lutego 2021. Administratorzy powinni włączyć kolejną metodę dla użytkowników, którzy nadal korzystają z dwukierunkowej wiadomości SMS.

Powiadomienia e-mail i powiadomienia dotyczące Service Health Azure Portal (wyskakujących powiadomień w portalu) zostały wysłane do odpowiednich administratorów w dniu 8 grudnia 2020 i 28 stycznia 2021. Alerty zostały przekazane do ról kontroli RBAC właściciela, współwłaściciela, administrator i administrator usługi, które są powiązane z subskrypcjami. Jeśli wykonano już następujące czynności, nie jest wymagane żadne działanie.

## <a name="required-actions"></a>Wymagane akcje

1. Włącz aplikację mobilną dla użytkowników, jeśli jeszcze jej nie zrobiono. Aby uzyskać więcej informacji, zobacz [Włączanie uwierzytelniania aplikacji mobilnych za pomocą serwera usługi MFA](howto-mfaserver-deploy-mobileapp.md).
1. Powiadom użytkowników końcowych, aby odwiedzali [Portal użytkowników](howto-mfaserver-deploy-userportal.md) serwera usługi MFA w celu aktywowania aplikacji mobilnej. [Aplikacja Microsoft Authenticator](https://www.microsoft.com/en-us/account/authenticator) jest zalecaną opcją weryfikacji, ponieważ jest bardziej bezpieczna niż dwukierunkowa wiadomość SMS. Aby uzyskać więcej informacji, zobacz [czas, który można rozłożyć na transporty telefoniczne na potrzeby uwierzytelniania](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/it-s-time-to-hang-up-on-phone-transports-for-authentication/ba-p/1751752).
1. Zmień ustawienia użytkownika z dwukierunkowej wiadomości tekstowej na aplikację mobilną jako domyślną metodę.

## <a name="faq"></a>Często zadawane pytania

### <a name="what-if-i-dont-change-the-default-method-from-two-way-sms-to-the-mobile-app"></a>Co zrobić, jeśli nie zmienimy domyślnej metody z dwukierunkowego SMS na aplikację mobilną?
Dwukierunkowa wiadomość SMS kończy się niepowodzeniem po 24 lutego 2021. Użytkownicy będą widzieć błąd podczas próby zalogowania się i przekazanie MFA.

### <a name="how-do-i-change-the-user-settings-from-two-way-text-message-to-mobile-app"></a>Jak mogę zmienić ustawienia użytkownika z dwukierunkowej wiadomości tekstowej na aplikację mobilną?

Należy zmienić ustawienia użytkownika, wykonując następujące czynności:

1. Na serwerze usługi MFA odfiltruj listę użytkowników w postaci dwukierunkowej wiadomości tekstowej.
1. Wybierz pozycję Wszyscy użytkownicy.
1. Otwórz okno dialogowe Edytowanie użytkowników.
1. Zmień użytkowników z wiadomości SMS na aplikację mobilną.

   ![Zrzut ekranu przedstawiający użytkowników końcowych](media/how-to-authentication-two-way-sms-unsupported/end-users.png)

### <a name="do-my-users-need-to-take-any-action-if-yes-how"></a>Czy moi użytkownicy muszą podejmować jakieś działania? Jeśli tak, jak?
Tak. Użytkownicy końcowi muszą odwiedzić określony Portal użytkowników serwera MFA, aby aktywować aplikację mobilną, jeśli nie zostały one jeszcze zrobione. Po wykonaniu kroku 3 wszyscy użytkownicy, którzy nie odwiedzali portalu użytkowników w celu skonfigurowania aplikacji mobilnej, nie będą uruchamiać logowania do momentu odwiedzenia się portalu użytkowników w celu ponownego zarejestrowania.

### <a name="what-if-my-users-cant-install-the-mobile-app-what-other-options-do-they-have"></a>Co zrobić, jeśli moi użytkownicy nie mogą zainstalować aplikacji mobilnej? Jakie są inne opcje?
Alternatywą dla dwukierunkowej wiadomości SMS lub aplikacji mobilnej jest połączenie telefoniczne. Jednak aplikacja Microsoft Authenticator jest zalecaną metodą weryfikacji.

### <a name="will-one-way-sms-be-deprecated-as-well"></a>Czy jest to jednokierunkowa wiadomość SMS?
Nie, tylko dwukierunkowa wiadomość SMS jest przestarzała. W przypadku serwera usługi MFA jednokierunkowa wiadomość SMS działa w ramach podzestawu scenariuszy:

- Adapter AD FS
- Uwierzytelnianie usług IIS (wymaga portalu i konfiguracji użytkownika)
- Promień (wymaga, aby klienci usługi RADIUS mogli obsługiwać wyzwanie dostępu oraz używany protokół PAP)

Istnieją pewne ograniczenia dotyczące używania jednokierunkowej wiadomości SMS, która umożliwia aplikacji mobilnej lepszą alternatywę, ponieważ nie wymaga monitu o kod weryfikacyjny.
Jeśli nadal chcesz używać jednokierunkowej wiadomości SMS w niektórych scenariuszach, możesz pozostawić te zaznaczone, ale zmienić sekcję **Ustawienia firmy** , a następnie pozycję Karta **ogólna** **Domyślnie wiadomość tekstowa** , a **nie dwukierunkowo** .  Na koniec w przypadku używania synchronizacji katalogów, która jest domyślnie ustawiona na wiadomość SMS, należy zmienić ją na One-Way, a nie dwukierunkową.

### <a name="how-can-i-check-which-users-are-still-using-two-way-sms"></a>Jak mogę sprawdzić, którzy użytkownicy nadal korzystają z dwukierunkowej wiadomości SMS?
Aby wyświetlić listę tych użytkowników, **Uruchom serwer usługi MFA**, wybierz sekcję **Użytkownicy** , kliknij pozycję **Filtruj listę użytkowników** i odfiltruj **wiadomość tekstową dwukierunkowo**.

### <a name="how-do-we-hide-two-way-sms-as-an-option-in-the-mfa-portal-to-prevent-users-from-selecting-it-in-the-future"></a>Jak ukryć dwukierunkową wiadomość SMS jako opcję w portalu usługi MFA, aby uniemożliwić użytkownikom Zaznaczanie jej w przyszłości?
W portalu użytkowników serwera usługi MFA kliknij pozycję **Ustawienia**, a następnie usuń **wiadomość tekstową** , aby była niedostępna. Ta sama wartość jest prawdziwa w sekcji **AD FS** , jeśli używasz AD FS do rejestracji użytkownika.

