---
title: Problemy z logowaniem do aplikacji z poziomu panelu dostępu | Microsoft Docs
description: Jak rozwiązywać problemy z uzyskiwaniem dostępu do aplikacji z poziomu panelu dostępu Microsoft Azure AD w witrynie myapps.microsoft.com
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
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c8d2c1ba156ba4e3c937ced6b81950859e1c095
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84759593"
---
# <a name="problems-signing-in-to-an-application-from-the-access-panel"></a>Problemy z logowaniem do aplikacji z poziomu panelu dostępu

Panel dostępu jest portalem opartym na sieci Web, który umożliwia użytkownikowi z kontem służbowym w usłudze Azure Active Directory (Azure AD) wyświetlanie i uruchamianie aplikacji opartych na chmurze, do których administrator usługi Azure AD udzielił im dostępu. 

Te aplikacje są konfigurowane w imieniu użytkownika w portalu usługi Azure AD. Aplikacja musi być poprawnie skonfigurowana i przypisana do użytkownika lub grupy, do której należy użytkownik, aby wyświetlić aplikację w panelu dostępu.

Typ aplikacji widocznych dla użytkownika można podzielić na następujące kategorie:

-   Aplikacje pakietu Office 365

-   Aplikacje firmy Microsoft i innych firm skonfigurowane z logowaniem jednokrotnym opartym na Federacji

-   Aplikacje logowania jednokrotnego oparte na hasłach

-   Aplikacje z istniejącymi rozwiązaniami logowania jednokrotnego

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

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Wymagania dotyczące przeglądarki dla panelu dostępu

Panel dostępu wymaga przeglądarki obsługującej język JavaScript i ma włączoną obsługę CSS. Aby korzystać z logowania jednokrotnego opartego na hasłach (SSO) w panelu dostępu, rozszerzenie panelu dostępu musi być zainstalowane w przeglądarce użytkownika. To rozszerzenie jest pobierane automatycznie, gdy użytkownik wybierze aplikację, która jest skonfigurowana dla logowania jednokrotnego opartego na hasłach.

W przypadku logowania jednokrotnego opartego na haśle przeglądarki użytkownika końcowego mogą:

-   Internet Explorer 8, 9, 10, 11 — w systemie Windows 7 lub nowszym

-   Microsoft Edge w systemie Windows 10 w wersji rocznicowej lub nowszej

-   Chrome — w systemie Windows 7 lub nowszym oraz w systemie MacOS X lub nowszym

-   Firefox 26,0 lub nowszy — w systemie Windows XP z dodatkiem SP2 lub nowszym oraz na Mac OS X 10,6 lub nowszym

## <a name="how-to-install-the-access-panel-browser-extension"></a>Jak zainstalować rozszerzenie przeglądarki panelu dostępu

Aby zainstalować rozszerzenie przeglądarki panelu dostępu, wykonaj następujące czynności:

1.  Otwórz [panel dostępu](https://myapps.microsoft.com) w jednej z obsługiwanych przeglądarek i zaloguj się jako **użytkownik** w usłudze Azure AD.

2.  Kliknij pozycję **hasło — logowanie JEDNOkrotne** w panelu dostępu.

3.  W wierszu polecenia z monitem o zainstalowanie oprogramowania wybierz pozycję **Zainstaluj teraz**.

4.  W zależności od używanej przeglądarki nastąpi przekierowanie do linku pobierania. **Dodaj** rozszerzenie do przeglądarki.

5.  Jeśli w przeglądarce zostanie wyświetlone pytanie, wybierz opcję **włączenia** lub **zezwolenia** na rozszerzenie.

6.  Po zainstalowaniu **Uruchom ponownie** sesję przeglądarki.

7.  Zaloguj się do panelu dostępu i sprawdź, czy możesz **uruchamiać** aplikacje logowania jednokrotnego

Możesz również pobrać rozszerzenie dla przeglądarki Chrome i Microsoft Edge z linków bezpośrednich poniżej:

-   [Rozszerzenie panelu dostępu dla programu Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Rozszerzenie panelu dostępu do przeglądarki Microsoft Edge](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84)

## <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Jak skonfigurować federacyjne Logowanie jednokrotne dla aplikacji w galerii usługi Azure AD

Wszystkie aplikacje w galerii usługi Azure AD z włączoną funkcją logowania jednokrotnego dla firm mają dostępny samouczek krok po kroku. Aby uzyskać szczegółowe wskazówki krok po kroku, możesz uzyskać dostęp do [listy samouczków dotyczących integrowania aplikacji SaaS z Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) .

Aby skonfigurować aplikację z galerii usługi Azure AD, należy wykonać następujące:

-   Dodawanie aplikacji z galerii usługi Azure AD

-   [Konfigurowanie wartości metadanych aplikacji w usłudze Azure AD (adres URL logowania, identyfikator, adres URL odpowiedzi)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Wybierz identyfikator użytkownika i Dodaj atrybuty użytkownika do wysłania do aplikacji](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Pobierz metadane i certyfikat usługi Azure AD](#download-the-azure-ad-metadata-or-certificate)

-   [Konfigurowanie wartości metadanych usługi Azure AD w aplikacji (adres URL logowania, wystawca, adres URL wylogowania i certyfikat)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   Przypisywanie użytkowników do aplikacji

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Dodawanie aplikacji z galerii usługi Azure AD

Aby dodać aplikację z galerii usługi Azure AD, wykonaj następujące czynności:

1.  Otwórz [Azure Portal](https://portal.azure.com) i zaloguj się jako **administrator globalny** lub **współadministrator**

2.  Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3.  Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4.  Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5.  Kliknij przycisk **Dodaj** w prawym górnym rogu w okienku aplikacje dla **przedsiębiorstw** .

6.  W polu tekstowym **Wprowadź nazwę** w sekcji **Dodaj z galerii** wpisz nazwę aplikacji.

7.  Wybierz aplikację, którą chcesz skonfigurować do logowania jednokrotnego.

8.  Przed dodaniem aplikacji można zmienić jej nazwę z pola tekstowego **Nazwa** .

9.  Kliknij przycisk **Dodaj** , aby dodać aplikację.

Po krótkim czasie zobaczysz okienko Konfiguracja aplikacji.

### <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Konfigurowanie logowania jednokrotnego dla aplikacji z galerii usługi Azure AD

Aby skonfigurować Logowanie jednokrotne dla aplikacji, wykonaj następujące czynności:

1. <span id="_Hlk477187909" class="anchor"><span id="_Hlk477001983" class="anchor"></span></span>Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.**

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3. Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4. Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5. Kliknij pozycję **wszystkie aplikacje** , aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, która ma być wyświetlana w tym miejscu, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje** i ustaw opcję **Pokaż** na **wszystkie aplikacje.**

6. Wybierz aplikację, dla której chcesz skonfigurować Logowanie jednokrotne.

7. Po załadowaniu aplikacji kliknij pozycję **Logowanie** jednokrotne w menu nawigacji po lewej stronie aplikacji.

8. Wybierz pozycję **Logowanie oparte** na protokole SAML z listy rozwijanej **tryb** .

9. Wprowadź wymagane wartości w polu **domena i adresy URL.** Te wartości należy uzyskać od dostawcy aplikacji.

   1. Aby skonfigurować aplikację jako logowanie jednokrotne zainicjowane przez usługę SP, adres URL logowania jest wartością wymaganą. W przypadku niektórych aplikacji identyfikator jest również wymaganą wartością.

   2. Aby skonfigurować aplikację jako zainicjowaną przez dostawcy tożsamości Logowanie jednokrotne, adres URL odpowiedzi jest wartością wymaganą. W przypadku niektórych aplikacji identyfikator jest również wymaganą wartością.

10. **Opcjonalne:** aby wyświetlić wartości niewymagane, kliknij pozycję **Pokaż zaawansowane ustawienia adresu URL** .

11. W polu **atrybuty użytkownika**wybierz unikatowy identyfikator dla użytkowników na liście rozwijanej **Identyfikator użytkownika** .

12. **Opcjonalnie:** kliknij przycisk **Wyświetl i edytuj wszystkie inne atrybuty użytkownika** , aby edytować atrybuty, które mają być wysyłane do aplikacji w tokenie SAML, gdy użytkownik loguje się.

    Aby dodać atrybut:

    1. Kliknij pozycję **Dodaj atrybut**. Wprowadź **nazwę** i wybierz **wartość** z listy rozwijanej.

    2. Kliknij przycisk **Zapisz.** Nowy atrybut zostanie wyświetlony w tabeli.

13. Kliknij pozycję **Konfiguruj &lt; nazwę &gt; aplikacji** , aby uzyskać dostęp do dokumentacji dotyczącej sposobu konfigurowania logowania jednokrotnego w aplikacji. Ponadto masz adresy URL metadanych i certyfikat wymagany do skonfigurowania logowania jednokrotnego za pomocą aplikacji.

14. Kliknij przycisk **Zapisz** , aby zapisać konfigurację.

15. Przypisz użytkowników do aplikacji.

### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Wybierz identyfikator użytkownika i Dodaj atrybuty użytkownika do wysłania do aplikacji

Aby wybrać identyfikator użytkownika lub dodać atrybuty użytkownika, wykonaj następujące czynności:

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.**

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3. Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4. Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5. Kliknij pozycję **wszystkie aplikacje** , aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, którą chcesz wyświetlić w tym miejscu, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje** i ustaw opcję **Pokaż** na **wszystkie aplikacje.**

6. Wybierz aplikację, dla której skonfigurowano Logowanie jednokrotne.

7. Po załadowaniu aplikacji kliknij pozycję **Logowanie** jednokrotne w menu nawigacji po lewej stronie aplikacji.

8. W sekcji **atrybuty użytkownika** wybierz unikatowy identyfikator dla użytkowników na liście rozwijanej **Identyfikator użytkownika** . Wybrana opcja musi być zgodna z oczekiwaną wartością w aplikacji w celu uwierzytelnienia użytkownika.

   >[!NOTE]
   >Usługa Azure AD wybierz format atrybutu NameID (identyfikator użytkownika) w oparciu o wybraną wartość lub format żądany przez aplikację w elemencie SAML AuthRequest. Aby uzyskać więcej informacji, zapoznaj się z artykułem Logowanie jednokrotne [protokołu SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) w sekcji NameIDPolicy.
   >
   >

9. Aby dodać atrybuty użytkownika, kliknij przycisk **Wyświetl i edytuj wszystkie inne atrybuty użytkownika** , aby edytować atrybuty, które mają być wysyłane do aplikacji w tokenie SAML, gdy użytkownik loguje się.

   Aby dodać atrybut:

   1. Kliknij pozycję **Dodaj atrybut**. Wprowadź **nazwę** i wybierz **wartość** z listy rozwijanej.

   2. Kliknij przycisk **Zapisz.** Nowy atrybut zostanie wyświetlony w tabeli.

### <a name="download-the-azure-ad-metadata-or-certificate"></a>Pobierz metadane lub certyfikat usługi Azure AD

Aby pobrać metadane aplikacji lub certyfikat z usługi Azure AD, wykonaj następujące czynności:

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.**

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3. Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4. Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5. Kliknij pozycję **wszystkie aplikacje** , aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, która ma być wyświetlana w tym miejscu, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje** i ustaw opcję **Pokaż** na **wszystkie aplikacje.**

6. Wybierz aplikację, dla której skonfigurowano Logowanie jednokrotne.

7. Po załadowaniu aplikacji kliknij pozycję **Logowanie** jednokrotne w menu nawigacji po lewej stronie aplikacji.

8. Przejdź do sekcji **certyfikat podpisywania SAML** , a następnie kliknij pozycję **Pobierz** wartość kolumny. W zależności od tego, co aplikacja wymaga skonfigurowania logowania jednokrotnego, zobaczysz opcję pobrania XML metadanych lub certyfikatu.

   Usługa Azure AD nie udostępnia adresu URL do pobrania metadanych. Metadane można pobrać tylko jako plik XML.

## <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Jak skonfigurować federacyjne Logowanie jednokrotne dla aplikacji spoza galerii

Aby skonfigurować aplikację spoza galerii, musisz mieć usługę Azure AD w wersji Premium, a aplikacja obsługuje protokół SAML 2,0. Aby uzyskać więcej informacji na temat wersji usługi Azure AD, zobacz [Cennik usługi Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

-   Konfigurowanie wartości metadanych aplikacji w usłudze Azure AD (adres URL logowania, identyfikator, adres URL odpowiedzi)

-   [Wybierz identyfikator użytkownika i Dodaj atrybuty użytkownika do wysłania do aplikacji](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Pobierz metadane i certyfikat usługi Azure AD](#download-the-azure-ad-metadata-or-certificate)

-   Konfigurowanie wartości metadanych usługi Azure AD w aplikacji (adres URL logowania, wystawca, adres URL wylogowania i certyfikat)

### <a name="configure-the-applications-metadata-values-in-azure-ad-sign-on-url-identifier-reply-url"></a>Konfigurowanie wartości metadanych aplikacji w usłudze Azure AD (adres URL logowania, identyfikator, adres URL odpowiedzi)

Aby skonfigurować Logowanie jednokrotne dla aplikacji, która nie znajduje się w galerii usługi Azure AD, wykonaj następujące czynności:

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.**

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3. Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4. Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5. Kliknij przycisk **Dodaj** w prawym górnym rogu w okienku aplikacje dla **przedsiębiorstw** .

6. Kliknij pozycję **aplikacja niebędąca galerią** w sekcji **Dodaj własną aplikację** .

7. Wprowadź nazwę aplikacji w polu tekstowym **Nazwa** .

8. Kliknij przycisk **Dodaj** , aby dodać aplikację.

9. Po załadowaniu aplikacji kliknij pozycję **Logowanie** jednokrotne w menu nawigacji po lewej stronie aplikacji.

10. Wybierz pozycję **Logowanie oparte na protokole SAML** na liście rozwijanej **tryb**

11. Wprowadź wymagane wartości w polu **domena i adresy URL.** Te wartości należy uzyskać od dostawcy aplikacji.

    1. Aby skonfigurować aplikację jako zainicjowaną przez dostawcy tożsamości, wprowadź adres URL odpowiedzi i identyfikator.

    2. **Opcjonalne:** Aby skonfigurować aplikację jako logowanie jednokrotne zainicjowane przez usługę SP, adres URL logowania jest wartością wymaganą.

12. W polu **atrybuty użytkownika**wybierz unikatowy identyfikator dla użytkowników na liście rozwijanej **Identyfikator użytkownika** .

13. **Opcjonalnie:** kliknij przycisk **Wyświetl i edytuj wszystkie inne atrybuty użytkownika** , aby edytować atrybuty, które mają być wysyłane do aplikacji w tokenie SAML, gdy użytkownik loguje się.

    Aby dodać atrybut:

    1. Kliknij pozycję **Dodaj atrybut**. Wprowadź **nazwę** i wybierz **wartość** z listy rozwijanej.

    2. Kliknij przycisk **Zapisz.** Nowy atrybut zostanie wyświetlony w tabeli.

14. Kliknij pozycję **Konfiguruj &lt; nazwę &gt; aplikacji** , aby uzyskać dostęp do dokumentacji dotyczącej sposobu konfigurowania logowania jednokrotnego w aplikacji. Ponadto masz adresy URL i certyfikaty usługi Azure AD wymagane dla aplikacji.

### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Wybierz identyfikator użytkownika i Dodaj atrybuty użytkownika do wysłania do aplikacji

Aby wybrać identyfikator użytkownika lub dodać atrybuty użytkownika, wykonaj następujące czynności:

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.**

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3. Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4. Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5. Kliknij pozycję **wszystkie aplikacje** , aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, która ma być wyświetlana w tym miejscu, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje** i ustaw opcję **Pokaż** na **wszystkie aplikacje.**

6. Wybierz aplikację, dla której skonfigurowano Logowanie jednokrotne.

7. Po załadowaniu aplikacji kliknij pozycję **Logowanie** jednokrotne w menu nawigacji po lewej stronie aplikacji.

8. W sekcji **atrybuty użytkownika** wybierz unikatowy identyfikator dla użytkowników na liście rozwijanej **Identyfikator użytkownika** . Wybrana opcja musi być zgodna z oczekiwaną wartością w aplikacji w celu uwierzytelnienia użytkownika.

   >[!NOTE]
   >Usługa Azure AD wybierz format atrybutu NameID (identyfikator użytkownika) w oparciu o wybraną wartość lub format żądany przez aplikację w elemencie SAML AuthRequest. Aby uzyskać więcej informacji, zapoznaj się z artykułem Logowanie jednokrotne [protokołu SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) w sekcji NameIDPolicy.
   >
   >

9. Aby dodać atrybuty użytkownika, kliknij przycisk **Wyświetl i edytuj wszystkie inne atrybuty użytkownika** , aby edytować atrybuty, które mają być wysyłane do aplikacji w tokenie SAML, gdy użytkownik loguje się.

   Aby dodać atrybut:

   1. kliknij pozycję **Dodaj atrybut**. Wprowadź **nazwę** i wybierz **wartość** z listy rozwijanej.

   2 kliknij pozycję **Zapisz.** Nowy atrybut zostanie wyświetlony w tabeli.

### <a name="download-the-azure-ad-metadata-or-certificate"></a>Pobierz metadane lub certyfikat usługi Azure AD

Aby pobrać metadane aplikacji lub certyfikat z usługi Azure AD, wykonaj następujące czynności:

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.**

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3. Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4. Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5. Kliknij pozycję **wszystkie aplikacje** , aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, która ma być wyświetlana w tym miejscu, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje** i ustaw opcję **Pokaż** na **wszystkie aplikacje.**

6. Wybierz aplikację, dla której skonfigurowano Logowanie jednokrotne.

7. Po załadowaniu aplikacji kliknij pozycję **Logowanie** jednokrotne w menu nawigacji po lewej stronie aplikacji.

8. Przejdź do sekcji **certyfikat podpisywania SAML** , a następnie kliknij pozycję **Pobierz** wartość kolumny. W zależności od tego, co aplikacja wymaga skonfigurowania logowania jednokrotnego, zobaczysz opcję pobrania XML metadanych lub certyfikatu.

   Usługa Azure AD nie udostępnia adresu URL do pobrania metadanych. Metadane można pobrać tylko jako plik XML.

## <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Jak skonfigurować Logowanie jednokrotne przy użyciu hasła dla aplikacji z galerii usługi Azure AD

Aby skonfigurować aplikację z galerii usługi Azure AD, należy wykonać następujące:

-   Dodawanie aplikacji z galerii usługi Azure AD

-   Konfigurowanie aplikacji do logowania jednokrotnego hasła

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Dodawanie aplikacji z galerii usługi Azure AD

Aby dodać aplikację z galerii usługi Azure AD, wykonaj następujące czynności:

1.  Otwórz [Azure Portal](https://portal.azure.com) i zaloguj się jako **administrator globalny** lub **współadministrator**

2.  Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3.  Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4.  Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5.  Kliknij przycisk **Dodaj** w prawym górnym rogu w okienku aplikacje dla **przedsiębiorstw** .

6.  W polu tekstowym **Wprowadź nazwę** w sekcji **Dodaj z galerii** wpisz nazwę aplikacji.

7.  Wybierz aplikację, dla której chcesz skonfigurować logowanie jednokrotne

8.  Przed dodaniem aplikacji można zmienić jej nazwę z pola tekstowego **Nazwa** .

9.  Kliknij przycisk **Dodaj** , aby dodać aplikację.

Po krótkim czasie zobaczysz okienko Konfiguracja aplikacji.

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

9. Przypisz użytkowników do aplikacji.

10. Dodatkowo można także podać poświadczenia w imieniu użytkownika, wybierając wiersze użytkowników i klikając opcję **zaktualizuj poświadczenia** i wprowadzając nazwę użytkownika i hasło w imieniu użytkowników. W przeciwnym razie użytkownicy otrzymają monit o wprowadzenie poświadczeń podczas uruchamiania.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Jak skonfigurować Logowanie jednokrotne przy użyciu hasła dla aplikacji spoza galerii

Aby skonfigurować aplikację z galerii usługi Azure AD, należy wykonać następujące:

-   [Dodawanie aplikacji spoza galerii](#add-a-non-gallery-application)

-   [Konfigurowanie aplikacji do logowania jednokrotnego hasła](#configure-the-application-for-password-single-sign-on)

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

6. Wybierz aplikację, dla której chcesz skonfigurować Logowanie jednokrotne.

7. Po załadowaniu aplikacji kliknij pozycję **Logowanie** jednokrotne w menu nawigacji po lewej stronie aplikacji.

8. Wybierz pozycję **Logowanie oparte na haśle.**

9. Wprowadź **adres URL logowania**. Jest to adres URL, pod którym użytkownicy wprowadzają nazwę użytkownika i hasło w celu zalogowania się. Upewnij się, że pola logowania są widoczne pod adresem URL.

10. Przypisz użytkowników do aplikacji.

11. Dodatkowo można także podać poświadczenia w imieniu użytkownika, wybierając wiersze użytkowników i klikając opcję **zaktualizuj poświadczenia** i wprowadzając nazwę użytkownika i hasło w imieniu użytkowników. W przeciwnym razie użytkownicy otrzymają monit o wprowadzenie poświadczeń podczas uruchamiania.

## <a name="how-to-assign-a-user-to-an-application-directly"></a>Jak bezpośrednio przypisać użytkownika do aplikacji

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

