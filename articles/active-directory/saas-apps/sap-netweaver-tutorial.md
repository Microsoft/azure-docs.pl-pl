---
title: 'Samouczek: Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z sap NetWeaver | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i oprogramowaniem SAP NetWeaver.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 1b9e59e3-e7ae-4e74-b16c-8c1a7ccfdef3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 700f2ca4d46b3483531fa0784cb78699befb20ca
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78897747"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-netweaver"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z usługą SAP NetWeaver

W tym samouczku dowiesz się, jak zintegrować sap NetWeaver z usługą Azure Active Directory (Azure AD). Po zintegrowaniu SAP NetWeaver z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do SAP NetWeaver.
* Włącz użytkownikom automatyczne logowanie do usługi SAP NetWeaver za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją logowania jednokrotnego SAP NetWeaver(SSO).
* Oprogramowanie SAP NetWeaver w wersji co najmniej 7.20

## <a name="scenario-description"></a>Opis scenariusza

SAP NetWeaver obsługuje zarówno **SAML** **(SP zainicjowane SSO)** i **OAuth**. W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym. 

> [!NOTE]
> Skonfiguruj aplikację w saml lub w OAuth zgodnie z wymaganiami organizacyjnymi. 

## <a name="adding-sap-netweaver-from-the-gallery"></a>Dodawanie oprogramowania SAP NetWeaver z galerii

Aby skonfigurować integrację oprogramowania SAP NetWeaver z usługą Azure AD, należy dodać oprogramowanie SAP NetWeaver z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **SAP NetWeaver** w polu wyszukiwania.
1. Wybierz **SAP NetWeaver** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-netweaver"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla usługi SAP NetWeaver

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą sap NetWeaver przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik sytuować dla użytkownika sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w sap NetWeaver.

Aby skonfigurować i przetestować sytuasz usługi Azure AD za pomocą sap NetWeaver, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj sytuasz usługi Azure AD,](#configure-azure-ad-sso)** aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD,](#create-an-azure-ad-test-user)** aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby włączyć B.Simon do korzystania z usługi Azure AD logowania jednokrotnego.
1. **[Skonfiguruj SAP NetWeaver za pomocą SAML,](#configure-sap-netweaver-using-saml)** aby skonfigurować ustawienia logowania sytego po stronie aplikacji.
    1. **[Utwórz użytkownika testowego SAP NetWeaver,](#create-sap-netweaver-test-user)** aby mieć odpowiednik B.Simon w SAP NetWeaver, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Przetestuj sytą próbę sycącą,](#test-sso)** aby sprawdzić, czy konfiguracja działa.
1. **[Skonfiguruj SAP NetWeaver dla OAuth,](#configure-sap-netweaver-for-oauth)** aby skonfigurować ustawienia OAuth po stronie aplikacji.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w oprogramowaniu SAP NetWeaver, wykonaj następujące kroki:

1. Otwórz nowe okno przeglądarki internetowej i zaloguj się do witryny firmy SAP NetWeaver jako administrator

1. Upewnij się, że usługi **http** i **https** są aktywne i że w kodzie transakcji **SMICM** są przypisane odpowiednie porty.

1. Zaloguj się do klienta biznesowego sap system (T01), gdzie logowanie sytograficzne jest wymagane i aktywować zarządzanie sesjami zabezpieczeń HTTP.

    a. Przejdź do sekcji **SICF_SESSIONS** kodu transakcji. Pokazuje ona wszystkie odpowiednie parametry profilu z bieżącymi wartościami. Wyglądają one podobnie do poniższych:-
    ```
    login/create_sso2_ticket = 2
    login/accept_sso2_ticket = 1
    login/ticketcache_entries_max = 1000
    login/ticketcache_off = 0  login/ticket_only_by_https = 0 
    icf/set_HTTPonly_flag_on_cookies = 3
    icf/user_recheck = 0  http/security_session_timeout = 1800
    http/security_context_cache_size = 2500
    rdisp/plugin_auto_logout = 1800
    rdisp/autothtime = 60
    ```
    >[!NOTE]
    > Dostosuj te parametry zgodnie z wymaganiami organizacji — powyższe parametry są tu podane jedynie jako przykład.

    b. W razie potrzeby dostosuj parametry, w profilu instancji/domyślnym systemu SAP i uruchom ponownie system SAP.

    d. Kliknij dwukrotnie odpowiedniego klienta, aby włączyć sesję zabezpieczeń HTTP.

    ![Link do pobierania certyfikatu](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_profileparameter.png)

    d. Aktywuj poniższe usługi SICF:
    ```
    /sap/public/bc/sec/saml2
    /sap/public/bc/sec/cdc_ext_service
    /sap/bc/webdynpro/sap/saml2
    /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
    ```
1. Przejdź do kodu transakcji **SAML2** w kliencie biznesowym systemu SAP [T01/122]. Spowoduje to otwarcie interfejsu użytkownika w przeglądarce. W tym przykładzie dla klienta biznesowego systemu SAP przyjmujemy wartość 122.

    ![Link do pobierania certyfikatu](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_sapbusinessclient.png)

1. Podaj nazwę użytkownika i hasło, które należy wprowadzić w interfejsie użytkownika, a następnie kliknij pozycję **Edit** (Edytuj).

    ![Link do pobierania certyfikatu](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_userpwd.png)

1. W polu **Provider Name** (Nazwa dostawcy) zastąp ciąg T01122 ciągiem `http://T01122` i kliknij pozycję **Save** (Zapisz).

    > [!NOTE]
    > Domyślnie nazwa dostawcy `<sid><client>` są jako format, ale usługa `<protocol>://<name>`Azure AD oczekuje nazwy `https://<sid><client>` w formacie , zalecając zachowanie nazwy dostawcy, aby umożliwić wiele aparatów SAP NetWeaver ABAP skonfigurować w usłudze Azure AD.

    ![Link do pobierania certyfikatu](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_providername.png)

1. **Generowanie metadanych dostawcy usługi** — po skonfigurowaniu ustawień **dostawcy lokalnego** i **zaufanych dostawców** w interfejsie użytkownika SAML 2.0 następnym krokiem będzie wygenerowanie pliku metadanych dostawcy usług (który zawiera wszystkie ustawienia, kontekst uwierzytelniania i inne konfiguracje w systemie SAP). Gdy plik zostanie wygenerowany, należy przekazać go w usłudze Azure AD.

    ![Link do pobierania certyfikatu](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_generatesp.png)

    a. Przejdź do **karty dostawcy lokalnego**.

    b. Kliknij pozycję **Metadata** (Metadane).

    d. Zapisz wygenerowany **plik XML metadanych** na komputerze i przekaż go w sekcji **Podstawowa konfiguracja SAML,** aby automatycznie wypełnić wartości **identyfikatora** i **odpowiedzi adresu URL** w witrynie Azure portal.

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)— na stronie integracji aplikacji **SAP NetWeaver** znajdź sekcję **Zarządzaj** i wybierz **opcję Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML,** jeśli chcesz skonfigurować aplikację w trybie inicjowanym **przez IDP,** wykonaj następujący krok:

    a. Kliknij **opcję Przekaż plik metadanych,** aby przekazać plik **metadanych usługodawcy,** który został uzyskany wcześniej.

    b. Kliknij **logo folderu**, aby wybrać plik metadanych, a następnie kliknij pozycję **Przekaż**.

    d. Po pomyślnym przekazaniu pliku metadanych wartości **Identyfikator** i **Adres URL odpowiedzi** zostaną automatycznie wypełnione w polach tekstowych sekcji **Podstawowa konfiguracja protokołu SAML**, jak pokazano poniżej:

    d. W polu **tekstowym Podpisywania adresu URL** wpisz adres URL, używając następującego wzorca:`https://<your company instance of SAP NetWeaver>`

    > [!NOTE]
    > Zaobserwowaliśmy, że niektórzy klienci zgłaszają błąd polegający na tym, że adres URL odpowiedzi skonfigurowany dla ich wystąpienia jest nieprawidłowy. Jeśli wystąpi taki błąd, możesz użyć następującego skryptu programu PowerShell jako obejścia w celu ustawienia prawidłowego adresu URL odpowiedzi dla wystąpienia:
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > Identyfikator obiektu ServicePrincipal musisz najpierw ustawić samodzielnie lub możesz również przekazać go w tym miejscu.

1. Aplikacja SAP NetWeaver oczekuje potwierdzeń SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe Atrybuty użytkownika.

    ![image](common/edit-attribute.png)

1. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej, i wykonaj następujące czynności:

    a. Kliknij **ikonę Edytuj**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![image](./media/sapnetweaver-tutorial/nameidattribute.png)

    ![image](./media/sapnetweaver-tutorial/nameidattribute1.png)

    b. Z listy **Przekształcenie** wybierz pozycję **ExtractMailPrefix()**.

    d. Z listy **Parametr 1** wybierz **user.userprincipalname**.

    d. Kliknij pozycję **Zapisz**.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** znajdź kod **XML metadanych federacji** i wybierz pozycję **Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

   ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie sap NetWeaver** skopiuj odpowiednie adresy URL na podstawie wymagań.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w witrynie Azure portal o nazwie B.Simon.

1. Z lewego okienka w witrynie Azure portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **pozycję Nowy użytkownik** u góry ekranu.
1. We właściwościach **Użytkownika** wykonaj następujące kroki:
    1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
    1. W polu **Nazwa użytkownika** username@companydomain.extensionwprowadź pole . Na przykład `B.Simon@contoso.com`.
    1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
    1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do SAP NetWeaver.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **SAP NetWeaver**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-sap-netweaver-using-saml"></a>Konfigurowanie sap NetWeaver przy użyciu SAML

1. Zaloguj się do systemu SAP i przejdź do kodu transakcji SAML2. Spowoduje to otwarcie nowego okna przeglądarki z ekranem konfiguracji protokołu SAML.

2. Aby skonfigurować punkty końcowe dla zaufanego dostawcy tożsamości (Azure AD), przejdź na kartę **Trusted Providers** (Zaufani dostawcy).

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_samlconfig.png)

3. Naciśnij pozycję **Add** (Dodaj) i wybierz polecenie **Upload Metadata File** (Przekaż plik metadanych) z menu kontekstowego.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_uploadmetadata.png)

4. Przekaż plik metadanych, który został pobrany z witryny Azure Portal.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_metadatafile.png)

5. Na następnym ekranie wpisz nazwę aliasu. Na przykład aadsts i naciśnij **przycisk Dalej,** aby kontynuować.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_aliasname.png)

6. Upewnij się, że w polu **Digest Algorithm** (Algorytm porządkowania ) znajduje się wartość **SHA-256** i że nie są wymagane żadne zmiany, a następnie naciśnij pozycję **Next** (Dalej).

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_identityprovider.png)

7. Na stronie **Single Sign-On Endpoints** (Punkty końcowe logowania jednokrotnego) użyj opcji **HTTP POST** i kliknij przycisk **Next** (Dalej), aby kontynuować.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect.png)

8. Na stronie **Single Logout Endpoints** (Punkty końcowe wylogowania jednokrotnego) wybierz opcję **HTTPRedirect** i kliknij przycisk **Next** (Dalej), aby kontynuować.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect1.png)

9. Na stronie **Artifact Endpoints** (Punkty końcowe artefaktu) naciśnij przycisk **Next** (Dalej), aby kontynuować.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_artifactendpoint.png)

10. Na stronie **Authentication Requirements** (Wymagania dotyczące uwierzytelniania) kliknij przycisk **Finish** (Zakończ).

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_authentication.png)

11. Przejdź do zakładki**Federacja tożsamości** **zaufanego dostawcy** > (od dołu ekranu). Kliknij pozycję **Edytuj**.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_trustedprovider.png)

12. Kliknij przycisk **Add** (Dodaj) na karcie **Identity Federation** (Federacja tożsamości) (dolne okno).

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_addidentityprovider.png)

13. W wyskakującym oknie wybierz **pozycję Nieokreślony** z **obsługiwanych formatów NameID** i kliknij przycisk OK.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameid.png)

14. Należy zauważyć, że **źródło identyfikatora użytkownika** i wartości **trybu mapowania identyfikatora użytkownika** określają łącze między użytkownikiem SAP a roszczeniem usługi Azure AD.  

    #### <a name="scenario-sap-user-to-azure-ad-user-mapping"></a>Scenariusz: Mapowanie użytkowników usługi Azure AD użytkownika użytkownika usługi SAP.

    a. Zrzut ekranu przedstawiający szczegóły identyfikatora nazwy w systemie SAP.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/nameiddetails.png)

    b. Zrzut ekranu przedstawiający wymagane oświadczenia z usługi Azure AD.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/claimsaad1.png)

    #### <a name="scenario-select-sap-user-id-based-on-configured-email-address-in-su01-in-this-case-email-id-should-be-configured-in-su01-for-each-user-who-requires-sso"></a>Scenariusz: wybierz identyfikator użytkownika SAP na podstawie skonfigurowanych adresów e-mail w SU01. W takim przypadku identyfikator wiadomości e-mail powinien być skonfigurowany w su01 dla każdego użytkownika, który wymaga podania sytego.

    a.  Zrzut ekranu przedstawiający szczegóły identyfikatora nazwy w systemie SAP.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameiddetails1.png)

    b. Zrzut ekranu przedstawiający wymagane oświadczenia z usługi Azure AD.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/claimsaad2.png)

15. Kliknij przycisk **Zapisz**, a następnie kliknij przycisk **Włącz**, aby włączyć dostawcę tożsamości.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/configuration1.png)

16. Po wyświetleniu monitu kliknij przycisk **OK**.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/configuration2.png)

    ### <a name="create-sap-netweaver-test-user"></a>Tworzenie użytkownika testowego oprogramowania SAP NetWeaver

    W tej sekcji utworzysz użytkownika o nazwie B.simon w SAP NetWeaver. Aby dodać użytkownika na platformie SAP NetWeaver, skontaktuj się z zespołem ekspertów ds. systemu SAP w swojej organizacji lub z odpowiednim partnerem firmy SAP.

## <a name="test-sso"></a>Test SSO

1. Po aktywowaniu usługi Azure AD jako dostawcy tożsamości spróbuj uzyskać dostęp do poniższego adresu URL, aby sprawdzić działanie logowania jednokrotnego (nie zostanie wyświetlony monit o nazwę użytkownika i hasło)

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    (lub) użyj poniższego adresu URL

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    > [!NOTE]
    > Zastąp ciąg „sapurl” faktyczną nazwą hosta systemu SAP.

2. Powyższy adres URL powinien przenieść Cię na pokazany poniżej ekran. Jeśli poniższa strona zostanie wyświetlona, oznacza to, że konfiguracja logowania jednokrotnego usługi Azure AD została przeprowadzona pomyślnie.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/testingsso.png)

3. Jeśli zostanie wyświetlony monit o nazwę użytkownika i hasło, zdiagnozuj problem, włączając śledzenie przy użyciu poniższego adresu URL

    `https://<sapurl>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#`

## <a name="configure-sap-netweaver-for-oauth"></a>Konfigurowanie SAP NetWeaver dla OAuth

1. Proces sap udokumentowane jest dostępny w lokalizacji: [NetWeaver Gateway Service Enabling i OAuth 2.0 Scope Creation](https://wiki.scn.sap.com/wiki/display/Security/NetWeaver+Gateway+Service+Enabling+and+OAuth+2.0+Scope+Creation)

2. Przejdź do SPRO i znajdź **pozycję Aktywuj i obsługa usług**.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/oauth01.png)

3. W tym przykładzie chcemy połączyć `DAAG_MNGGRP` usługę OData: z OAuth do usługi Azure AD SSO. Użyj wyszukiwania nazw usługi technicznej `DAAG_MNGGRP` dla usługi i aktywować, `green` jeśli nie jest jeszcze aktywny, już (poszukaj stanu w zakładce węzły ICF). Upewnij się, że alias systemowy (podłączony system wewnętrznej bazy danych, w którym usługa jest faktycznie uruchomiona) jest poprawny.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/oauth02.png)

    * Następnie kliknij przycisk **OAuth** na górnym pasku przycisku i przypisz `scope` (zachowaj domyślną nazwę jako oferowaną).

4. W naszym przykładzie `DAAG_MNGGRP_001`zakres jest , jest generowany z nazwy usługi przez automatyczne dodanie liczby. Raport `/IWFND/R_OAUTH_SCOPES` może służyć do zmiany nazwy zakresu lub tworzenia ręcznie.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/oauth03.png)

    > [!NOTE]
    > Komunikat `soft state status is not supported` – może być ignorowany, ponieważ nie ma problemu. Aby uzyskać więcej informacji, zapoznaj się [tutaj](https://help.sap.com/doc/saphelp_nw74/7.4.16/1e/c60c33be784846aad62716b4a1df39/content.htm?no_cache=true)

### <a name="create-a-service-user-for-the-oauth-20-client"></a>Tworzenie użytkownika usługi dla klienta OAuth 2.0

1. OAuth2 używa `service ID` a, aby uzyskać token dostępu dla użytkownika końcowego w jego imieniu. Ważne ograniczenie przez projekt OAuth: `OAuth 2.0 Client ID` musi `username` być identyczny z klientem OAuth 2.0 używa do logowania podczas żądania tokenu dostępu. W związku z tym w naszym przykładzie mamy zamiar zarejestrować klienta OAuth 2.0 z nazwą CLIENT1 i jako warunek wstępny użytkownik o tej samej nazwie (CLIENT1) musi istnieć w systemie SAP i że użytkownik będziemy skonfigurować do użycia przez aplikację poleconą. 

2. Rejestrując Klienta OAuth używamy `SAML Bearer Grant type`.

    >[!NOTE]
    >Aby uzyskać więcej informacji, zapoznaj się z OAuth 2.0 Rejestracja klienta dla SAML Bearer Grant Type [tutaj](https://wiki.scn.sap.com/wiki/display/Security/OAuth+2.0+Client+Registration+for+the+SAML+Bearer+Grant+Type)

3. tcod: SU01 / create `System type` user CLIENT1 as and assign password, save it as need to provide the credential to the API programmer, who should burn it with the username to the calling code. Nie należy przypisywać profilu ani roli.

### <a name="register-the-new-oauth-20-client-id-with-the-creation-wizard"></a>Zarejestruj nowy identyfikator klienta OAuth 2.0 za pomocą kreatora tworzenia

1. Aby zarejestrować nową transakcję rozpoczęcia **klienta OAuth 2.0** **SOAUTH2**. Transakcja wyświetli przegląd klientów OAuth 2.0, które zostały już zarejestrowane. Wybierz **pozycję Utwórz,** aby uruchomić kreatora dla nowego klienta OAuth o nazwie CLIENT1w tym przykładzie.

2. Przejdź do T-Code: **SOAUTH2** i podaj opis, a następnie kliknij **następny**.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/oauth04.png)

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/oauth05.png)

3. Wybierz już dodany **identyfikator SAML2 — usługa Azure AD** z listy rozwijanej i zapisz.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/oauth06.png)

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/oauth07.png)

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/oauth08.png)

4. Kliknij **przycisk Dodaj** w obszarze przypisania zakresu, aby dodać poprzednio utworzony zakres:`DAAG_MNGGRP_001`

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/oauth09.png)

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/oauth10.png)

5. Kliknij **przycisk Zakończ**.

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj usługę SAP NetWeaver z usługą Azure AD](https://aad.portal.azure.com/)