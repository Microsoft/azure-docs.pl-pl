---
title: Problemy z konfigurowaniem logowania jednokrotnego dla aplikacji w galerii usługi Azure AD
description: Poznaj typowe problemy występujące podczas konfigurowania logowania jednokrotnego hasła dla aplikacji, które są już wymienione w galerii aplikacji usługi Azure AD
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89fda2657a68fc8a3fd293c0c6001a71f5970548
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763520"
---
# <a name="problem-configuring-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Problem podczas konfigurowania logowania jednokrotnego przy użyciu hasła dla aplikacji z galerii usługi Azure AD

Ten artykuł ułatwia zapoznanie się z typowymi problemami występującymi podczas konfigurowania **logowania** jednokrotnego za pomocą aplikacji w galerii usługi Azure AD.

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>Poświadczenia są wypełniane, ale rozszerzenie nie przesyła ich

Ten problem zwykle występuje, gdy dostawca aplikacji zmienił stronę logowania ostatnio, aby dodać pole, zmienić identyfikator używany do wykrywania pól username i Password lub zmodyfikować sposób działania logowania dla swojej aplikacji. Na szczęście firma Microsoft może współpracować z dostawcami aplikacji, aby szybko rozwiązać te problemy.

Chociaż firma Microsoft ma technologie wykrywające przerwy w integracji, mogą nie być możliwe do wyszukania problemów od razu lub problemy są rozwiązywane nieco czasu. W przypadku, gdy jedna z tych integracji nie działa prawidłowo, należy otworzyć sprawę pomocy technicznej, aby można było ją rozwiązać tak szybko, jak to możliwe.

**Jeśli jesteś w kontakcie z dostawcą aplikacji,** Wyślij je, aby firma Microsoft mogła współpracować z nimi w celu natywnej integracji swojej aplikacji z Azure Active Directory. Aby rozpocząć pracę, możesz wysłać dostawcę do [listy aplikacji w galerii aplikacji Azure Active Directory](../azuread-dev/howto-app-gallery-listing.md) .

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>Poświadczenia są wypełnione i przesłane, ale strona wskazuje, że poświadczenia są niepoprawne

Aby rozwiązać ten problem, należy najpierw wykonać następujące czynności:

- Użytkownik musi najpierw próbować **zalogować się do witryny sieci Web aplikacji bezpośrednio** przy użyciu poświadczeń przechowywanych dla nich.

  * Jeśli zalogujesz się, kliknij przycisk **Aktualizuj poświadczenia** na **kafelku aplikacji** w sekcji **aplikacje** w [panelu dostęp do aplikacji](https://myapps.microsoft.com/) , aby zaktualizować je do najnowszej znanej roboczej nazwy użytkownika i hasła.

  * Jeśli ty lub inny administrator przypisał poświadczenia dla tego użytkownika, Znajdź przypisanie aplikacji użytkownika lub grupy, przechodząc do karty **użytkownicy & grupy** aplikacji, wybierając przypisanie i klikając przycisk **Aktualizuj poświadczenia** .

- Jeśli użytkownik przypisał własne poświadczenia, użytkownik musi **upewnić się, że hasło nie wygasło w aplikacji** , a jeśli tak, **zaktualizuj swoje wygasłe hasło** , logując się do aplikacji bezpośrednio.

  * Po zaktualizowaniu hasła w aplikacji, należy poprosić użytkownika o kliknięcie przycisku **Aktualizuj poświadczenia** na **kafelku aplikacji** w sekcji **aplikacje** w [panelu dostęp do aplikacji](https://myapps.microsoft.com/) , aby zaktualizować je do najnowszej znanej roboczej nazwy użytkownika i hasła.

  * Jeśli ty lub inny administrator przypisał poświadczenia dla tego użytkownika, Znajdź przypisanie aplikacji użytkownika lub grupy, przechodząc do karty **użytkownicy & grupy** aplikacji, wybierając przypisanie i klikając przycisk **Aktualizuj poświadczenia** .

- Aby użytkownik zaktualizował rozszerzenie przeglądarki panelu dostępu, wykonaj poniższe czynności w sekcji [jak zainstalować rozszerzenie przeglądarki panelu dostępu](#how-to-install-the-access-panel-browser-extension) .

- Upewnij się, że rozszerzenie przeglądarki panelu dostępu jest uruchomione i włączone w przeglądarce użytkownika.

- Upewnij się, że użytkownicy nie próbują zalogować się do aplikacji z poziomu panelu dostępu w **trybie incognito, InPrivate lub Private**. Rozszerzenie panelu dostępu nie jest obsługiwane w tych trybach.

Na wypadek, gdyby poprzednie sugestie nie zadziałały, może się zdarzyć, że na stronie aplikacji wystąpił zmiana, która tymczasowo przerwał integrację aplikacji z usługą Azure AD. Na przykład może się to zdarzyć, gdy dostawca aplikacji wprowadza skrypt na stronie, która zachowuje się inaczej w przypadku ręcznych danych wejściowych programu vs, które powodują, że automatyczna integracja, taka jak własna, może zostać przerwana. Na szczęście firma Microsoft może współpracować z dostawcami aplikacji, aby szybko rozwiązać te problemy.

Firma Microsoft ma technologie umożliwiające automatyczne wykrywanie w przypadku przerwania integracji aplikacji, jednak może nie być możliwe znalezienie problemów od razu lub problemy mogą zostać naprawione. Jeśli integracja nie działa prawidłowo, można otworzyć przypadek pomocy technicznej w celu jego ustalenia tak szybko, jak to możliwe. 

Oprócz tego, **Jeśli kontaktuje się z dostawcą aplikacji,** **Wysyłaj je do nas** , aby możemy z nich współpracować, aby w sposób natywny zintegrować swoją aplikację z Azure Active Directory. Aby rozpocząć pracę, możesz wysłać dostawcę do [listy aplikacji w galerii aplikacji Azure Active Directory](../azuread-dev/howto-app-gallery-listing.md) .

## <a name="the-extension-works-in-chrome-and-firefox-but-not-in-internet-explorer"></a>Rozszerzenie działa w przeglądarce Chrome i Firefox, ale nie w programie Internet Explorer

Istnieją dwa główne przyczyny tego problemu:

- W zależności od ustawień zabezpieczeń włączonych w programie Internet Explorer, jeśli witryna sieci Web nie jest częścią **zaufanej strefy**, czasami można zablokować wykonywanie skryptu dla aplikacji.

  *  Aby rozwiązać ten problem, użytkownik powinien **dodać witrynę sieci Web aplikacji** do listy **zaufanych witryn** w **ustawieniach zabezpieczeń programu Internet Explorer**. Aby uzyskać szczegółowe instrukcje, można wysłać użytkowników do artykułu " [jak dodać witrynę do listy zaufanych witryn](https://answers.microsoft.com/en-us/ie/forum/ie9-windows_7/how-do-i-add-a-site-to-my-trusted-sites-list/98cc77c8-b364-e011-8dfc-68b599b31bf5) ".

- W rzadkich przypadkach sprawdzanie poprawności zabezpieczeń w programie Internet Explorer może czasami spowodować spowolnienie ładowania strony niż wykonanie naszego skryptu.

  * Niestety, ta sytuacja może się różnić w zależności od wersji przeglądarki, szybkości komputera lub odwiedzonej witryny. W tym przypadku zalecamy, aby skontaktować się z pomocą techniczną, aby można było naprawić integrację tej konkretnej aplikacji.

Oprócz tego, **Jeśli kontaktuje się z dostawcą aplikacji,** **Wysyłaj je do nas** , aby możemy z nich współpracować, aby w sposób natywny zintegrować swoją aplikację z Azure Active Directory. Aby rozpocząć pracę, możesz wysłać dostawcę do [listy aplikacji w galerii aplikacji Azure Active Directory](../azuread-dev/howto-app-gallery-listing.md) .

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>Sprawdź, czy strona logowania aplikacji ostatnio się zmieniła lub wymaga dodatkowego pola

Jeśli strona logowania aplikacji została zmieniona drastycznie, czasami może to spowodować przerwanie naszych integracji. Przykładem jest to, że dostawca aplikacji dodaje do swoich środowisk pole logowania, CAPTCHA lub uwierzytelnianie wieloskładnikowe. Na szczęście firma Microsoft może współpracować z dostawcami aplikacji, aby szybko rozwiązać te problemy.

Firma Microsoft ma technologie umożliwiające automatyczne wykrywanie w przypadku przerwania integracji aplikacji, jednak może nie być możliwe znalezienie problemów od razu lub problemy mogą zostać naprawione. Jeśli integracja nie działa prawidłowo, można otworzyć przypadek pomocy technicznej w celu jego ustalenia tak szybko, jak to możliwe. 

Oprócz tego, **Jeśli kontaktuje się z dostawcą aplikacji,** **Wysyłaj je do nas** , aby możemy z nich współpracować, aby w sposób natywny zintegrować swoją aplikację z Azure Active Directory. Aby rozpocząć pracę, możesz wysłać dostawcę do [listy aplikacji w galerii aplikacji Azure Active Directory](../azuread-dev/howto-app-gallery-listing.md) .

## <a name="how-to-install-the-access-panel-browser-extension"></a>Jak zainstalować rozszerzenie przeglądarki panelu dostępu

Aby zainstalować rozszerzenie przeglądarki panelu dostępu, wykonaj następujące czynności:

1.  Otwórz [panel dostępu](https://myapps.microsoft.com) w jednej z obsługiwanych przeglądarek i zaloguj się jako **użytkownik** w usłudze Azure AD.

2.  Kliknij pozycję **hasło — logowanie JEDNOkrotne** w panelu dostępu.

3.  W wierszu polecenia z monitem o zainstalowanie oprogramowania wybierz pozycję **Zainstaluj teraz**.

4.  W oparciu o przeglądarkę nastąpi przekierowanie do linku pobierania. **Dodaj** rozszerzenie do przeglądarki.

5.  Jeśli w przeglądarce zostanie wyświetlone pytanie, wybierz opcję **włączenia** lub **zezwolenia** na rozszerzenie.

6.  Po zainstalowaniu **Uruchom ponownie** sesję przeglądarki.

7.  Zaloguj się do panelu dostępu i sprawdź, czy możesz **uruchamiać** aplikacje logowania jednokrotnego

Możesz również pobrać rozszerzenie dla przeglądarki Chrome i Firefox z linków bezpośrednich poniżej:

-   [Rozszerzenie panelu dostępu dla programu Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Rozszerzenie panelu dostępu Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="next-steps"></a>Następne kroki
[Zapewnianie logowania jednokrotnego do aplikacji za pomocą serwera proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md)

