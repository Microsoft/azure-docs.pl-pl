---
title: Problem z logowaniem do aplikacji w galerii usługi Azure AD skonfigurowany na potrzeby logowania jednokrotnego | Microsoft Docs
description: Jak rozwiązywać problemy z aplikacją galerii usługi Azure AD, która jest skonfigurowana do logowania jednokrotnego hasła.
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
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6eebde3a7c6163b7faf92be193fe442cd5b74d2c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84759117"
---
# <a name="sign-in-problems-with-an-azure-ad-gallery-app-configured-for-sso"></a>Problemy z logowaniem przy użyciu aplikacji z galerii usługi Azure AD skonfigurowanej na potrzeby logowania jednokrotnego

Panel dostępu jest portalem opartym na sieci Web. Umożliwia ona użytkownikom mającym Konta służbowe Azure Active Directory (Azure AD) na dostęp do aplikacji opartych na chmurze, dla których mają uprawnienia. Użytkownicy, którzy mają wersje usługi Azure AD, mogą również korzystać z funkcji samoobsługowego zarządzania grupami i aplikacjami za pomocą panelu dostępu.

Panel dostępu jest oddzielony od Azure Portal. Użytkownicy nie potrzebują subskrypcji platformy Azure do korzystania z panelu dostępu.

Aby można było korzystać z logowania jednokrotnego opartego na hasłach (SSO) w panelu dostępu, rozszerzenie panelu dostępu musi być zainstalowane w przeglądarce. Rozszerzenie jest pobierane automatycznie po wybraniu aplikacji skonfigurowanej do logowania jednokrotnego opartego na hasłach.

## <a name="browser-requirements-for-access-panel"></a>Wymagania dotyczące przeglądarki dla panelu dostępu

Panel dostępu wymaga przeglądarki obsługującej język JavaScript i ma włączoną obsługę CSS.

Następujące przeglądarki obsługują Logowanie jednokrotne oparte na haśle:

- Internet Explorer 8, 9, 10 i 11 w systemie Windows 7 lub nowszym

- Chrome w systemie Windows 7 lub nowszym albo w MacOS X lub nowszym

- Firefox 26,0 lub nowszy w systemie Windows XP z dodatkiem SP2 lub nowszym lub na Mac OS X 10,6 lub nowszym

>[!NOTE]
>Rozszerzenie logowania jednokrotnego oparte na hasłach staje się dostępne dla przeglądarki Microsoft Edge w systemie Windows 10, gdy obsługa rozszerzeń przeglądarek została dodana do programu Microsoft Edge.

## <a name="install-the-access-panel-browser-extension"></a>Zainstaluj rozszerzenie przeglądarki panelu dostępu

Wykonaj następujące kroki:

1. Otwórz [panel dostępu](https://myapps.microsoft.com) w obsługiwanej przeglądarce i zaloguj się jako użytkownik w usłudze Azure AD.

2. Wybierz pozycję hasło — z obsługą logowania jednokrotnego w panelu dostępu.

3. Po wyświetleniu monitu wybierz pozycję **Zainstaluj teraz**.

4. Nastąpi przekierowanie do linku pobierania w oparciu o przeglądarkę. Wybierz pozycję **Dodaj** , aby zainstalować rozszerzenie przeglądarki.

5. Jeśli zostanie wyświetlony monit, wybierz pozycję **Włącz** lub **Zezwalaj**.

6. Po zakończeniu instalacji uruchom ponownie przeglądarkę.

7.  Zaloguj się do panelu dostępu i sprawdź, czy możesz uruchamiać aplikacje obsługujące Logowanie jednokrotne.

Możesz również bezpośrednio pobrać rozszerzenia dla programu Chrome i Firefox, korzystając z następujących linków:

-   [Rozszerzenie panelu dostępu dla programu Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Rozszerzenie panelu dostępu Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Konfigurowanie zasad grupy dla programu Internet Explorer

Można skonfigurować zasady grupy, które umożliwiają zdalne instalowanie rozszerzenia panelu dostępu dla programu Internet Explorer na komputerach użytkowników.

Poniżej przedstawiono wymagania wstępne:

-   Należy skonfigurować [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx) , a komputery użytkowników muszą być przyłączone do domeny.

-   Masz uprawnienie "Edytuj ustawienia", aby edytować obiekt zasady grupy (GPO). Domyślnie członkowie następujących grup zabezpieczeń mają to uprawnienie: Administratorzy domeny, Administratorzy przedsiębiorstwa i właściciele zasady grupy Twórcy. [Dowiedz się więcej](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

Aby skonfigurować zasady grupy i wdrożyć je dla użytkowników, zobacz [jak wdrożyć rozszerzenie panelu dostępu dla programu Internet Explorer przy użyciu zasad grupy](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy).

## <a name="troubleshoot-access-panel-in-internet-explorer"></a>Rozwiązywanie problemów z panelem dostępu w programie Internet Explorer

Aby uzyskać dostęp do narzędzia diagnostycznego i instrukcje dotyczące konfigurowania rozszerzenia, zobacz [Rozwiązywanie problemów z rozszerzeniem panelu dostępu dla programu Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting).

## <a name="configure-password-sso-for-an-azure-ad-gallery-app"></a>Konfigurowanie logowania jednokrotnego dla aplikacji w galerii usługi Azure AD

Aby skonfigurować aplikację z galerii usługi Azure AD, należy wykonać następujące czynności:

-   Dodawanie aplikacji z galerii usługi Azure AD
-   [Skonfiguruj aplikację do logowania jednokrotnego hasła](#configure-the-app-for-password-sso)
-   [Przypisywanie użytkowników do aplikacji](#assign-users-to-the-app)

### <a name="add-the-app-from-the-azure-ad-gallery"></a>Dodawanie aplikacji z galerii usługi Azure AD

Wykonaj następujące kroki:

1. Otwórz [Azure Portal](https://portal.azure.com) i zaloguj się jako Administrator globalny lub współadministrator.

2. Wybierz pozycję **wszystkie usługi** w górnej części okienka nawigacji po lewej stronie, aby otworzyć rozszerzenie usługi Azure AD.

3. Wpisz **Azure Active Directory** w polu wyszukiwania filtru, a następnie wybierz pozycję **Azure Active Directory**.

4. Wybierz pozycję **aplikacje dla przedsiębiorstw** z okienka nawigacji usługi Azure AD.

5. Wybierz pozycję **Dodaj** w prawym górnym rogu okienka **aplikacje dla przedsiębiorstw** .

6. W sekcji **Dodaj z galerii** wpisz nazwę aplikacji w polu **Wprowadź nazwę** .

7. Wybierz aplikację, którą chcesz skonfigurować dla logowania jednokrotnego.

8. *Opcjonalne:* Przed dodaniem aplikacji można zmienić jej nazwę w polu **Nazwa** .

9. Kliknij przycisk **Dodaj**, aby dodać aplikację.

   Po krótkim opóźnieniu zobaczysz okienko Konfiguracja aplikacji.

### <a name="configure-the-app-for-password-sso"></a>Konfigurowanie aplikacji na potrzeby logowania jednokrotnego przy użyciu hasła

Wykonaj następujące kroki:

1. Otwórz [Azure Portal](https://portal.azure.com/) i zaloguj się jako Administrator globalny lub współadministrator.

2. Wybierz pozycję **wszystkie usługi** w górnej części okienka nawigacji po lewej stronie, aby otworzyć rozszerzenie usługi Azure AD.

3. Wpisz **Azure Active Directory** w polu wyszukiwania filtru, a następnie wybierz pozycję **Azure Active Directory**.

4. W okienku nawigacji usługi Azure AD wybierz pozycję **aplikacje dla przedsiębiorstw** .

5. Wybierz pozycję **wszystkie aplikacje** , aby wyświetlić listę aplikacji.

   > [!NOTE]
   > Jeśli nie widzisz potrzebnej aplikacji, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje**. Dla opcji **Pokaż** wybierz wartość "wszystkie aplikacje".

6. Wybierz aplikację, którą chcesz skonfigurować dla logowania jednokrotnego.

7. Po załadowaniu aplikacji wybierz pozycję **Logowanie** jednokrotne w okienku po lewej stronie aplikacji.

8. Wybierz tryb **logowania opartego na hasłach** .

9. Przypisz użytkowników do aplikacji.

10. Możesz również podać poświadczenia dla użytkowników. (W przeciwnym razie użytkownicy otrzymają monit o wprowadzenie poświadczeń podczas uruchamiania aplikacji). W tym celu wybierz wiersze użytkowników. Następnie wybierz pozycję **Aktualizuj poświadczenia** i wprowadź nazwy użytkowników i hasła.

### <a name="assign-users-to-the-app"></a>Przypisywanie użytkowników do aplikacji

Aby bezpośrednio przypisywać użytkowników do aplikacji, wykonaj następujące kroki:

1. Otwórz [Azure Portal](https://portal.azure.com/) i zaloguj się jako Administrator globalny.

2. Wybierz pozycję **wszystkie usługi** w obszarze Nawigacja, aby otworzyć rozszerzenie usługi Azure AD.

3. Wpisz **Azure Active Directory** w polu wyszukiwania filtru, a następnie wybierz pozycję **Azure Active Directory**.

4. W okienku nawigacji usługi Azure AD wybierz pozycję **aplikacje dla przedsiębiorstw** .

5. Wybierz pozycję **wszystkie aplikacje** , aby wyświetlić listę aplikacji.

   > [!NOTE]
   > Jeśli nie widzisz potrzebnej aplikacji, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje**. Dla opcji **Pokaż** wybierz wartość "wszystkie aplikacje".

6. Z listy wybierz aplikację, do której chcesz przypisać użytkownika.

7. Po załadowaniu aplikacji wybierz pozycję **Użytkownicy i grupy** w okienku nawigacji aplikacji po lewej stronie.

8. Wybierz pozycję **Dodaj** w górnej części listy **Użytkownicy i grupy** , aby otworzyć okienko **Dodaj przypisanie** .

9. Wybierz pozycję **Użytkownicy i grupy** w okienku **Dodaj przypisanie** .

10. W polu **Wyszukaj według nazwy lub adresu e-mail** wpisz pełną nazwę lub adres e-mail użytkownika, który chcesz przypisać.

11. Umieść kursor nad użytkownikiem na liście. Zaznacz pole wyboru obok zdjęcia lub logo profilu użytkownika, aby dodać tego użytkownika do **wybranej** listy.

12. *Opcjonalne:* Aby dodać innego użytkownika, wpisz inną nazwę lub adres e-mail w polu **Wyszukaj według nazwy lub adresu e-mail** , a następnie zaznacz pole wyboru, aby dodać tego użytkownika do **wybranej** listy.

13. Po zakończeniu wybierania użytkowników kliknij pozycję **Wybierz** , aby dodać je do listy użytkowników i grup przypisanych do aplikacji.

14. *Opcjonalne:* Kliknij pozycję **Wybierz rolę** w okienku **Dodaj przypisanie** , aby wybrać rolę, która ma zostać przypisana do wybranych użytkowników.

15. Wybierz pozycję **Przypisz** , aby przypisać aplikację do wybranych użytkowników.

    Po krótkim opóźnieniu użytkownicy będą mogli uzyskiwać dostęp do tych aplikacji z poziomu panelu dostępu.

## <a name="request-support"></a>Żądaj obsługi 
Jeśli podczas konfigurowania logowania jednokrotnego i przypisywania użytkowników zostanie wyświetlony komunikat o błędzie, Otwórz bilet pomocy technicznej. Dołącz możliwie najwięcej następujących informacji:

-   Identyfikator błędu korelacji
-   Nazwa UPN (adres e-mail użytkownika)
-   TenantID
-   Typ przeglądarki
-   Strefa czasowa i czas/czas wystąpienia błędu
-   Ślady programu Fiddler

## <a name="next-steps"></a>Następne kroki
[Zapewnianie logowania jednokrotnego do aplikacji za pomocą serwera proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md)
