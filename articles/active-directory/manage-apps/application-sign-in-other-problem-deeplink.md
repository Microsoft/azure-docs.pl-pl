---
title: Problemy z logowaniem do aplikacji przy użyciu linku bezpośredniego | Microsoft Docs
description: Jak rozwiązywać problemy z uzyskiwaniem dostępu do aplikacji z adresu URL głębokiego łącza za pomocą usługi Azure AD
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
ms.openlocfilehash: 3c68143dabd9b047a8d6cc37ccac770b7d954656
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/14/2020
ms.locfileid: "84759491"
---
# <a name="problems-signing-in-to-an-application-using-a-deeplink"></a>Problemy z logowaniem do aplikacji przy użyciu linku bezpośredniego

Panel dostępu jest portalem opartym na sieci Web, który umożliwia użytkownikowi z kontem służbowym w usłudze Azure Active Directory (Azure AD) wyświetlanie i uruchamianie aplikacji opartych na chmurze, do których administrator usługi Azure AD udzielił im dostępu. 

Te aplikacje są konfigurowane w imieniu użytkownika w portalu usługi Azure AD. Aplikacja musi być poprawnie skonfigurowana i przypisana do użytkownika lub grupy, do której należy użytkownik, aby wyświetlić aplikację w panelu dostępu.

Linki głębokie lub adresy URL dostępu użytkowników to linki, których użytkownicy mogą używać w celu uzyskania dostępu do aplikacji logowania jednokrotnego w przeglądarce. Przechodzenie do tego linku powoduje automatyczne zalogowanie użytkowników do aplikacji bez konieczności wcześniejszego przechodzenia do panelu dostępu. Jest to ten sam link, którego użytkownicy używają do uzyskiwania dostępu do tych aplikacji z poziomu uruchamiania aplikacji pakietu Office 365.

## <a name="general-issues-to-check-first"></a>Ogólne problemy do sprawdzenia w pierwszej kolejności

-   Upewnij się, że korzystasz z **przeglądarki** , która spełnia minimalne wymagania dotyczące panelu dostępu.

-   Upewnij się, że przeglądarka użytkownika dodała adres URL aplikacji do jej **zaufanych witryn**.

-   Upewnij się, że aplikacja została prawidłowo **skonfigurowana** .

-   Upewnij się, że konto użytkownika jest **włączone** do logowania.

-   Upewnij się, że konto użytkownika **nie jest zablokowane.**

-   Upewnij się, że **hasło użytkownika nie wygasło lub zostało zapomniane.**

-   Upewnij się, że **Multi-Factor Authentication** nie blokuje dostępu użytkownika.

-   Upewnij się, że **zasady dostępu warunkowego** lub zasady **ochrony tożsamości** nie blokują dostępu użytkownika.

-   Upewnij się, że **informacje kontaktowe uwierzytelniania** użytkownika są aktualne, aby zezwolić na wymuszanie zasad dostępu Multi-Factor Authentication lub warunkowego.

-   Upewnij się, że wyczyścisz również pliki cookie w przeglądarce i spróbujesz ponownie zalogować.

## <a name="checking-the-deeplink"></a>Sprawdzanie głębokiego łącza

Aby sprawdzić, czy masz poprawne głębokie łącza bezpośredniego, wykonaj następujące kroki:

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.**

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3. Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4. Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5. Kliknij pozycję **wszystkie aplikacje** , aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, która ma być wyświetlana w tym miejscu, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje** i ustaw opcję **Pokaż** na **wszystkie aplikacje.**

6. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.**

7. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

8. Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

9. Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

10. Kliknij pozycję **wszystkie aplikacje** , aby wyświetlić listę wszystkich aplikacji.

    * Jeśli nie widzisz aplikacji, która ma być wyświetlana w tym miejscu, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje** i ustaw opcję **Pokaż** na **wszystkie aplikacje.**

11. Wybierz aplikację, dla której ma zostać sprawdzone głębokie łącza.

12. Znajdź etykietę **adres URL dostępu użytkownika**. Ten adres URL powinien być zgodny z linkiem głębokiego łącza.

## <a name="how-to-install-the-access-panel-browser-extension"></a>Jak zainstalować rozszerzenie przeglądarki panelu dostępu

Aby zainstalować rozszerzenie przeglądarki panelu dostępu, wykonaj następujące kroki:

1.  Otwórz [panel dostępu](https://myapps.microsoft.com) w jednej z obsługiwanych przeglądarek i zaloguj się jako **użytkownik** w usłudze Azure AD.

2.  Kliknij pozycję **hasło — logowanie JEDNOkrotne** w panelu dostępu.

3.  W wierszu polecenia z monitem o zainstalowanie oprogramowania wybierz pozycję **Zainstaluj teraz**.

4.  W zależności od używanej przeglądarki nastąpi przekierowanie do linku pobierania. **Dodaj** rozszerzenie do przeglądarki.

5.  Jeśli w przeglądarce zostanie wyświetlone pytanie, wybierz opcję **włączenia** lub **zezwolenia** na rozszerzenie.

6.  Po zainstalowaniu **Uruchom ponownie** sesję przeglądarki.

7.  Zaloguj się do panelu dostępu i sprawdź, czy możesz **uruchamiać** aplikacje logowania jednokrotnego

Możesz również pobrać rozszerzenie dla przeglądarki Chrome i Firefox z tych bezpośrednich linków:

-   [Rozszerzenie panelu dostępu dla programu Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Rozszerzenie panelu dostępu Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Jak skonfigurować Logowanie jednokrotne przy użyciu hasła dla aplikacji z galerii usługi Azure AD

Aby skonfigurować aplikację z galerii usługi Azure AD, musisz:

-   [Dodawanie aplikacji z galerii usługi Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Konfigurowanie aplikacji do logowania jednokrotnego hasła](#configure-the-application-for-password-single-sign-on)

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Dodawanie aplikacji z galerii usługi Azure AD

Aby dodać aplikację z galerii usługi Azure AD, wykonaj następujące kroki:

1.  Otwórz [Azure Portal](https://portal.azure.com) i zaloguj się jako **administrator globalny** lub **współadministrator**.

2.  Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3.  Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4.  Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5.  Kliknij przycisk **Dodaj** w prawym górnym rogu w okienku aplikacje dla **przedsiębiorstw** .

6.  W polu tekstowym **Wprowadź nazwę** w sekcji **Dodaj z galerii** wpisz nazwę aplikacji.

7.  Wybierz aplikację, którą chcesz skonfigurować do logowania jednokrotnego.

8.  Przed dodaniem aplikacji można zmienić jej nazwę z pola tekstowego **Nazwa** .

9.  Aby dodać aplikację, kliknij przycisk **Dodaj**.

Po krótkim czasie będzie można zobaczyć okienko Konfiguracja aplikacji.

### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurowanie aplikacji do logowania jednokrotnego hasła

Aby skonfigurować Logowanie jednokrotne dla aplikacji, wykonaj następujące kroki:

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.**

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3. Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4. Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5. Kliknij pozycję **wszystkie aplikacje** , aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, która ma być wyświetlana w tym miejscu, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje** i ustaw opcję **Pokaż** na **wszystkie aplikacje.**

6. Wybierz aplikację, dla której chcesz skonfigurować Logowanie jednokrotne.

7. Po załadowaniu aplikacji kliknij pozycję **Logowanie** jednokrotne w menu nawigacji po lewej stronie aplikacji.

8. Wybierz pozycję **Logowanie oparte na haśle.**

9. [Przypisz użytkowników do aplikacji](#how-to-assign-a-user-to-an-application-directly).

10. Dodatkowo można także podać poświadczenia w imieniu użytkownika, wybierając wiersze użytkowników i klikając opcję **zaktualizuj poświadczenia** i wprowadzając nazwę użytkownika i hasło w imieniu użytkowników. W przeciwnym razie użytkownicy otrzymają monit o wprowadzenie poświadczeń podczas uruchamiania.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Jak skonfigurować Logowanie jednokrotne przy użyciu hasła dla aplikacji spoza galerii

Aby skonfigurować aplikację z galerii usługi Azure AD, musisz:

-   [Dodawanie aplikacji spoza galerii](#add-a-non-gallery-application)

-   [Konfigurowanie aplikacji do logowania jednokrotnego hasła](#configure-the-application-for-password-single-sign-on)

### <a name="add-a-non-gallery-application"></a>Dodawanie aplikacji spoza galerii

Aby dodać aplikację z galerii usługi Azure AD, wykonaj następujące kroki:

1.  Otwórz [Azure Portal](https://portal.azure.com) i zaloguj się jako **administrator globalny** lub **współadministrator**.

2.  Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3.  Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4.  Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5.  Kliknij przycisk **Dodaj** w prawym górnym rogu w okienku aplikacje dla **przedsiębiorstw** .

6.  Kliknij pozycję **aplikacja niebędąca galerią.**

7.  Wprowadź nazwę aplikacji w polu tekstowym **Nazwa** . Wybierz pozycję **Dodaj.**

Po krótkim czasie będzie można zobaczyć okienko Konfiguracja aplikacji.

### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurowanie aplikacji do logowania jednokrotnego hasła

Aby skonfigurować Logowanie jednokrotne dla aplikacji, wykonaj następujące kroki:

1.  Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.**

2.  Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3.  Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4.  Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5.  Kliknij pozycję **wszystkie aplikacje** , aby wyświetlić listę wszystkich aplikacji.

    1.  Jeśli nie widzisz aplikacji, która ma być wyświetlana w tym miejscu, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje** i ustaw opcję **Pokaż** na **wszystkie aplikacje.**

6.  Wybierz aplikację, dla której chcesz skonfigurować Logowanie jednokrotne.

7.  Po załadowaniu aplikacji kliknij pozycję **Logowanie** jednokrotne w menu nawigacji po lewej stronie aplikacji.

8.  Wybierz pozycję **Logowanie oparte na haśle.**

9.  Wprowadź **adres URL logowania**, adres URL, pod którym użytkownicy wprowadzają nazwę użytkownika i hasło, aby się zalogować. Upewnij się, że pola logowania są widoczne pod adresem URL.

10. Przypisz użytkowników do aplikacji.

11. Dodatkowo można także podać poświadczenia w imieniu użytkownika, wybierając wiersze użytkowników i klikając opcję **zaktualizuj poświadczenia** i wprowadzając nazwę użytkownika i hasło w imieniu użytkowników. W przeciwnym razie użytkownicy otrzymają monit o wprowadzenie poświadczeń podczas uruchamiania.

## <a name="how-to-assign-a-user-to-an-application-directly"></a>Jak bezpośrednio przypisać użytkownika do aplikacji

Aby bezpośrednio przypisać jednego lub większą liczbę użytkowników do aplikacji, wykonaj następujące kroki:

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

11. Umieść kursor nad **użytkownikiem** na liście, aby odsłonić **pole wyboru**. Aby dodać użytkownika do **wybranej** listy, kliknij pole wyboru obok zdjęcia lub logo profilu użytkownika.

12. **Opcjonalne:** Jeśli chcesz **dodać więcej niż jednego użytkownika**, wpisz inną **pełną nazwę** lub **adres e-mail** w polu **Wyszukaj według nazwy lub adresu e-mail** , a następnie kliknij pole wyboru, aby dodać tego użytkownika do **wybranej** listy.

13. Po zakończeniu wybierania użytkowników kliknij przycisk **Wybierz** , aby dodać do listy użytkowników i grup, które mają być przypisane do aplikacji.

14. **Opcjonalnie:** kliknij selektor **roli wybierz** w okienku **Dodaj przypisanie** , aby wybrać rolę, która ma zostać przypisana do wybranych użytkowników.

15. Kliknij przycisk **Przypisz** , aby przypisać aplikację do wybranych użytkowników.

Po krótkim czasie wybrane przez Ciebie użytkownicy będą mogli uruchamiać te aplikacje w panelu dostępu.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Jeśli te kroki rozwiązywania problemów nie rozwiążą problemu. 

Otwórz bilet pomocy technicznej z następującymi informacjami, jeśli są dostępne:

-   Identyfikator błędu korelacji

-   Nazwa UPN (adres e-mail użytkownika)

-   TenantID

-   Typ przeglądarki

-   Strefa czasowa i czas/przedział czasu w trakcie wystąpienia błędu

-   Ślady programu Fiddler

## <a name="next-steps"></a>Następne kroki
[Zapewnianie logowania jednokrotnego do aplikacji za pomocą serwera proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md)
