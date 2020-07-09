---
title: Zainstaluj rozszerzenie przeglądarki panelu dostępu do aplikacji — Azure AD
description: Rozwiązywanie typowych błędów napotkanych podczas instalowania rozszerzenia przeglądarki panelu dostępu.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/04/2018
ms.author: kenwith
ms.reviewer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2046c24220079a604792d07f3ebc3f6ef11e9c8a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84761089"
---
# <a name="install-the-access-panel-browser-extension"></a>Zainstaluj rozszerzenie przeglądarki panelu dostępu

Panel dostępu jest portalem opartym na sieci Web. Jeśli masz konto służbowe w usłudze Azure Active Directory (Azure AD), możesz użyć panelu dostępu do wyświetlania i uruchamiania aplikacji opartych na chmurze, do których administrator usługi Azure AD udzielił Ci dostępu. 

Jeśli używasz wersji usługi Azure AD, możesz również korzystać z funkcji samoobsługowego zarządzania grupami i aplikacjami za pośrednictwem panelu dostępu. 

Panel dostępu jest oddzielony od Azure Portal. Nie wymaga to posiadania subskrypcji platformy Azure.

## <a name="web-browser-requirements"></a>Wymagania dotyczące przeglądarki sieci Web

Panel dostępu wymaga co najmniej przeglądarki obsługującej język JavaScript i ma włączoną funkcję CSS. Aby zalogować się do aplikacji za pomocą logowania jednokrotnego na podstawie hasła w panelu dostępu, musisz mieć zainstalowane rozszerzenie panelu dostępu w przeglądarce. Rozszerzenie jest pobierane automatycznie po wybraniu aplikacji, która jest skonfigurowana dla logowania jednokrotnego opartego na hasłach.

W przypadku logowania jednokrotnego opartego na haśle można użyć dowolnej z następujących przeglądarek:

- **Microsoft Edge**: w systemie Windows 10 w wersji rocznicowej lub nowszej. 
- **Chrome**: w systemie Windows 7 lub nowszym oraz w systemie MacOS X lub nowszym.
- **Firefox 26,0 lub nowszy**: w systemie Windows XP z dodatkiem SP2 lub nowszym oraz na Mac OS X 10,6 lub nowszym.

## <a name="install-the-access-panel-browser-extension"></a>Zainstaluj rozszerzenie przeglądarki panelu dostępu

Aby zainstalować rozszerzenie przeglądarki panelu dostępu, wykonaj następujące czynności:

1.  W jednej z obsługiwanych przeglądarek Otwórz [panel dostępu](https://myapps.microsoft.com), a następnie zaloguj się jako użytkownik na koncie usługi Azure AD.

2.  Wybierz aplikację Logowanie jednokrotne oparte na haśle.

3.  Po wyświetleniu monitu wybierz pozycję **Zainstaluj teraz**.  
    Nastąpi przekierowanie do linku pobierania dla wybranej przeglądarki. 
    
4.  Wybierz pozycję **Dodaj**.

5.  Jeśli zostanie wyświetlony monit, **Włącz** lub **Zezwól** na rozszerzenie.

6.  Po zakończeniu instalacji uruchom ponownie przeglądarkę.

7.  Zaloguj się do panelu dostępu i sprawdź, czy możesz uruchamiać aplikacje logowania jednokrotnego oparte na hasłach.

Możesz również pobrać rozszerzenie dla przeglądarki Chrome i Microsoft Edge bezpośrednio z następujących lokacji:

- [Rozszerzenie Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)
- [Rozszerzenie Microsoft Edge](https://www.microsoft.com/en-us/p/my-apps-secure-sign-in-extension/9pc9sckkzk84)
- [Rozszerzenie Firefox](https://addons.mozilla.org/en-US/firefox/addon/access-panel-extension/)

## <a name="use-the-my-apps-secure-sign-in-extension"></a>Używanie rozszerzenia moje aplikacje bezpieczne logowanie
* Jeśli używasz adresu URL moje aplikacje inne niż `https://myapps.microsoft.com` , skonfiguruj domyślny adres URL, wykonując następujące czynności:
   1. Gdy *nie* zalogowano się do rozszerzenia, kliknij prawym przyciskiem myszy ikonę rozszerzenia.
   2. W menu wybierz **adres URL moje aplikacje**.
   3. Wybierz domyślny adres URL.
   4. Wybierz ikonę rozszerzenia.
   5. Aby zalogować się do rozszerzenia, wybierz pozycję **Zaloguj się, aby rozpocząć**.

* Aby zalogować się bezpośrednio do aplikacji z poziomu przeglądarki, wykonaj następujące czynności:
   1. Po zainstalowaniu rozszerzenia Zaloguj się do niego, wybierając pozycję **Zaloguj się, aby rozpocząć**.
   2. Zaloguj się do aplikacji przy użyciu adresu URL logowania.  
       Adres URL logowania jest zwykle adresem URL aplikacji, która wyświetla formularz logowania.
      Rozszerzenie powinno zmienić stan i poinformować, że hasło jest dostępne.
   3. Aby się zalogować, wybierz ikonę rozszerzenia.

* Aby uruchomić aplikację z poziomu rozszerzenia, wykonaj następujące czynności:
   1. Po zainstalowaniu rozszerzenia Zaloguj się do niego, wybierając pozycję **Zaloguj się, aby rozpocząć**.
   2. Wybierz ikonę rozszerzenia, aby otworzyć jego menu.
   3. Wyszukaj aplikację dostępną w portalu Moje aplikacje.
   4. Z listy wyników wyszukiwania wybierz aplikację.  
       Ostatnie trzy używane aplikacje są wyświetlane na liście **ostatnio używanych** skrótów.
       
* Aby użyć wewnętrznych adresów URL firmy podczas zdalnego, wykonaj następujące czynności:
    1. [Konfigurowanie serwera proxy aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-enable) w dzierżawie
    2. [Publikowanie aplikacji](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal) i adresu URL za pomocą serwera proxy aplikacji
    3. Zainstaluj rozszerzenie i zaloguj się do niego, wybierając pozycję Zaloguj się, aby rozpocząć
    4. Teraz możesz przejść do wewnętrznego adresu URL firmy, nawet gdy jest to zdalne

> [!NOTE]
> Powyższe opcje są dostępne tylko dla programów Microsoft Edge, Chrome i Firefox.

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Konfigurowanie zasad grupy dla programu Internet Explorer

Można skonfigurować zasady grupy, które umożliwiają zdalne instalowanie rozszerzenia panelu dostępu dla programu Internet Explorer na komputerach użytkowników.

Przed skonfigurowaniem zasad grupy upewnij się, że:

-   Skonfigurowano [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)i przyłączono komputery użytkowników do domeny.

-   Aby edytować obiekt zasady grupy (GPO), musisz mieć uprawnienia do *edycji ustawień* . Domyślnie to uprawnienie jest udzielane członkom następujących grup zabezpieczeń: Administratorzy domeny, Administratorzy przedsiębiorstwa i twórcy-właściciele zasad grupy.

Aby uzyskać instrukcje krok po kroku dotyczące konfigurowania zasad grupy i wdrażania ich dla użytkowników, zobacz [Wdrażanie rozszerzenia panelu dostępu dla programu Internet Explorer przy użyciu zasad grupy](deploy-access-panel-browser-extension.md).

## <a name="troubleshoot-the-access-panel-extension-in-internet-explorer"></a>Rozwiązywanie problemów z rozszerzeniem panelu dostępu w programie Internet Explorer

Aby uzyskać dostęp do narzędzia diagnostycznego i informacji na temat konfigurowania rozszerzenia programu Internet Explorer, zobacz [Rozwiązywanie problemów z rozszerzeniem panelu dostępu dla programu Internet Explorer](manage-access-panel-browser-extension.md).

> [!NOTE]
> Program Internet Explorer ma ograniczoną obsługę i nie otrzymuje już nowych aktualizacji oprogramowania. Zalecana przeglądarka to Microsoft Edge.

## <a name="if-the-preceding-steps-do-not-resolve-the-issue"></a>Jeśli powyższe kroki nie rozwiążą problemu

Otwórz bilet pomocy technicznej z następującymi informacjami, jeśli jest dostępny:

-   Identyfikator błędu korelacji
-   Nazwa UPN (adres e-mail użytkownika)
-   TenantID
-   Typ przeglądarki
-   Strefa czasowa i czas lub przedział czasu, gdy wystąpił błąd
-   Ślady programu Fiddler

## <a name="next-steps"></a>Następne kroki
[Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory?](what-is-single-sign-on.md)
