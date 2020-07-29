---
title: Logowanie jednokrotne oparte na hasłach (SSO) w panelu dostępu | Microsoft Docs
description: W tym artykule omówiono obszary problemów, które zapewniają wskazówki dotyczące rozwiązywania problemów związanych z logowaniem do aplikacji w galerii usługi Azure AD skonfigurowanych do logowania jednokrotnego hasła.
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
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd4a645038dfb49e7c79995b90b159bdc07bea4f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763775"
---
# <a name="problems-signing-in-to-an-azure-ad-gallery-application-configured-for-password-single-sign-on"></a>Problemy z logowaniem do aplikacji w galerii usługi Azure AD skonfigurowanej do logowania jednokrotnego hasła

Panel dostępu jest portalem opartym na sieci Web, który umożliwia użytkownikowi, który ma konto służbowe w usłudze Azure Active Directory (Azure AD) wyświetlanie i uruchamianie aplikacji opartych na chmurze, do których administrator usługi Azure AD udzielił im dostępu. Użytkownik, który ma wersje usługi Azure AD, może również korzystać z funkcji samoobsługowego zarządzania grupami i aplikacjami za pomocą panelu dostępu. Panel dostępu jest oddzielony od Azure Portal i nie wymaga, aby użytkownicy mieli subskrypcję platformy Azure.

Aby korzystać z logowania jednokrotnego opartego na hasłach (SSO) w panelu dostępu, rozszerzenie panelu dostępu musi być zainstalowane w przeglądarce użytkownika. To rozszerzenie jest pobierane automatycznie, gdy użytkownik wybierze aplikację, która jest skonfigurowana dla logowania jednokrotnego opartego na hasłach.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Wymagania dotyczące przeglądarki dla panelu dostępu

Panel dostępu wymaga przeglądarki obsługującej język JavaScript i ma włączoną obsługę CSS. Aby korzystać z logowania jednokrotnego opartego na hasłach (SSO) w panelu dostępu, rozszerzenie panelu dostępu musi być zainstalowane w przeglądarce użytkownika. To rozszerzenie jest pobierane automatycznie, gdy użytkownik wybierze aplikację, która jest skonfigurowana dla logowania jednokrotnego opartego na hasłach.

W przypadku logowania jednokrotnego opartego na haśle przeglądarki użytkownika końcowego mogą:

-   Internet Explorer 8, 9, 10, 11 — w systemie Windows 7 lub nowszym

-   Chrome — w systemie Windows 7 lub nowszym oraz w systemie MacOS X lub nowszym

-   Firefox 26,0 lub nowszy — w systemie Windows XP z dodatkiem SP2 lub nowszym oraz na Mac OS X 10,6 lub nowszym

>[!NOTE]
>Rozszerzenie logowania jednokrotnego oparte na hasłach staje się dostępne dla przeglądarki Microsoft Edge w systemie Windows 10, gdy rozszerzenia przeglądarek stają się obsługiwane w programie Microsoft Edge.
>
>

## <a name="how-to-install-the-access-panel-browser-extension"></a>Jak zainstalować rozszerzenie przeglądarki panelu dostępu

Aby zainstalować rozszerzenie przeglądarki panelu dostępu, wykonaj następujące czynności:

1.  Otwórz [panel dostępu](https://myapps.microsoft.com) w jednej z obsługiwanych przeglądarek i zaloguj się jako **użytkownik** w usłudze Azure AD.

2.  Kliknij pozycję **hasło — logowanie JEDNOkrotne** w panelu dostępu.

3.  W wierszu polecenia z monitem o zainstalowanie oprogramowania wybierz pozycję **Zainstaluj teraz**.

4.  W zależności od używanej przeglądarki do linku pobierania. **Dodaj** rozszerzenie do przeglądarki.

5.  Jeśli w przeglądarce zostanie wyświetlone pytanie, wybierz opcję **włączenia** lub **zezwolenia** na rozszerzenie.

6.  Po zainstalowaniu **Uruchom ponownie** sesję przeglądarki.

7.  Zaloguj się do panelu dostępu i sprawdź, czy możesz **uruchamiać** aplikacje logowania jednokrotnego

Możesz również pobrać rozszerzenie dla przeglądarki Chrome i Firefox z linków bezpośrednich poniżej:

-   [Rozszerzenie panelu dostępu dla programu Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Rozszerzenie panelu dostępu Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="setting-up-a-group-policy-for-internet-explorer"></a>Konfigurowanie zasad grupy dla programu Internet Explorer

Można skonfigurować zasady grupy, które umożliwiają zdalne instalowanie rozszerzenia panelu dostępu dla programu Internet Explorer na komputerach użytkowników.

Wymagania wstępne obejmują:

-   Skonfigurowano [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)i przyłączono komputery użytkowników do domeny.

-   Aby edytować obiekt zasady grupy (GPO), musisz mieć uprawnienie "Edytuj ustawienia". Domyślnie członkowie następujących grup zabezpieczeń mają to uprawnienie: Administratorzy domeny, Administratorzy przedsiębiorstwa i właściciele zasady grupy Twórcy. [Dowiedz się więcej](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

Postępuj zgodnie z samouczkiem [jak wdrożyć rozszerzenie panelu dostępu dla programu Internet Explorer przy użyciu zasady grupy](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy) , aby uzyskać instrukcje krok po kroku dotyczące konfigurowania zasad grupy i wdrażania ich dla użytkowników.

## <a name="troubleshoot-the-access-panel-in-internet-explorer"></a>Rozwiązywanie problemów z panelem dostępu w programie Internet Explorer

Postępuj zgodnie z instrukcjami w części [Rozwiązywanie problemów z rozszerzeniami panelu dostępu dla programu Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting) , aby uzyskać dostęp do narzędzia diagnostycznego i instrukcje krok po kroku dotyczące konfigurowania rozszerzenia dla programu IE.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Jak skonfigurować Logowanie jednokrotne przy użyciu hasła dla aplikacji spoza galerii

Aby skonfigurować aplikację z galerii usługi Azure AD, należy wykonać następujące:

-   [Dodawanie aplikacji spoza galerii](#add-a-non-gallery-application)

-   [Konfigurowanie aplikacji do logowania jednokrotnego hasła](#configure-the-application-for-password-single-sign-on)

-   [Przypisywanie użytkowników do aplikacji](#assign-users-to-the-application)

### <a name="add-a-non-gallery-application"></a>Dodawanie aplikacji spoza galerii

Aby dodać aplikację z galerii usługi Azure AD, wykonaj następujące czynności:

1.  Otwórz [Azure Portal](https://portal.azure.com) i zaloguj się jako **administrator globalny** lub **współadministrator**

2.  Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3.  Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4.  Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5.  Kliknij przycisk **Dodaj** w prawym górnym rogu w okienku aplikacje dla **przedsiębiorstw** .

6.  Kliknij pozycję **aplikacja niebędąca galerią.**

7.  Wprowadź nazwę aplikacji w polu tekstowym **Nazwa** . Wybierz pozycję **Dodaj.**

Po krótkim czasie będzie możliwe wyświetlenie okienka konfiguracja aplikacji.

### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurowanie aplikacji do logowania jednokrotnego hasła

Aby skonfigurować Logowanie jednokrotne dla aplikacji, wykonaj następujące czynności:

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.**

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3. Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4. Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5. Kliknij pozycję **wszystkie aplikacje** , aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, która ma być wyświetlana w tym miejscu, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje** i ustaw opcję **Pokaż** na **wszystkie aplikacje.**

6. Wybierz aplikację, dla której chcesz skonfigurować Logowanie jednokrotne

7. Po załadowaniu aplikacji kliknij pozycję **Logowanie** jednokrotne w menu nawigacji po lewej stronie aplikacji.

8. Wybierz pozycję **Logowanie oparte na haśle.**

9. Wprowadź **adres URL logowania**. Jest to adres URL, pod którym użytkownicy wprowadzają nazwę użytkownika i hasło, aby się zalogować. Upewnij się, że pola logowania są widoczne pod adresem URL.

10. Przypisz użytkowników do aplikacji.

11. Dodatkowo można także podać poświadczenia w imieniu użytkownika, wybierając wiersze użytkowników i klikając opcję **zaktualizuj poświadczenia** i wprowadzając nazwę użytkownika i hasło w imieniu użytkowników. W przeciwnym razie użytkownicy otrzymają monit o wprowadzenie poświadczeń podczas uruchamiania.

### <a name="assign-users-to-the-application"></a>Przypisywanie użytkowników do aplikacji

Aby bezpośrednio przypisać co najmniej jednego użytkownika do aplikacji, wykonaj następujące czynności:

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3. Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4. Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5. Kliknij pozycję **wszystkie aplikacje** , aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, która ma być wyświetlana w tym miejscu, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje** i ustaw opcję **Pokaż** na **wszystkie aplikacje.**

6. Wybierz aplikację, do której chcesz przypisać użytkownika z listy.

7. Po załadowaniu aplikacji kliknij pozycję **Użytkownicy i grupy** w menu nawigacji po lewej stronie aplikacji.

8. Kliknij przycisk **Dodaj** na górze listy **Użytkownicy i grupy** , aby otworzyć okienko **Dodaj przypisanie** .

9. Kliknij selektor **Użytkownicy i grupy** w okienku **Dodaj przypisanie** .

10. Wpisz **pełną nazwę** lub **adres e-mail** użytkownika, którego chcesz przypisać, w polu Wyszukaj **według nazwy lub adresu e-mail** .

11. Umieść kursor nad **użytkownikiem** na liście, aby odsłonić **pole wyboru**. Kliknij pole wyboru obok zdjęcia lub logo profilu użytkownika, aby dodać użytkownika do **wybranej** listy.

12. **Opcjonalne:** Jeśli chcesz **dodać więcej niż jednego użytkownika**, wpisz inną **pełną nazwę** lub **adres e-mail** w polu **Wyszukaj według nazwy lub adresu e-mail** , a następnie kliknij pole wyboru, aby dodać tego użytkownika do **wybranej** listy.

13. Po zakończeniu wybierania użytkowników kliknij przycisk **Wybierz** , aby dodać do listy użytkowników i grup, które mają być przypisane do aplikacji.

14. **Opcjonalnie:** kliknij selektor **roli wybierz** w okienku **Dodaj przypisanie** , aby wybrać rolę, która ma zostać przypisana do wybranych użytkowników.

15. Kliknij przycisk **Przypisz** , aby przypisać aplikację do wybranych użytkowników.

Po krótkim czasie wybrane przez Ciebie użytkownicy będą mogli uruchamiać te aplikacje w panelu dostępu.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Jeśli te kroki rozwiązywania problemów nie rozwiązują problemu

Otwórz bilet pomocy technicznej z następującymi informacjami, jeśli są dostępne:

-   Identyfikator błędu korelacji

-   Nazwa UPN (adres e-mail użytkownika)

-   TenantID

-   Typ przeglądarki

-   Strefa czasowa i czas/przedział czasu w trakcie wystąpienia błędu

-   Ślady programu Fiddler

## <a name="next-steps"></a>Następne kroki
[Zapewnianie logowania jednokrotnego do aplikacji za pomocą serwera proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md)

