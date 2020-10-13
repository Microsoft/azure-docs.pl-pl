---
title: 'Samouczek: Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) za pomocą oprogramowania SAP NetWeaver | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i oprogramowaniem SAP NetWeaver.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: jeedes
ms.openlocfilehash: d91ada217d54f424803abfeb31dcad237b5fe05c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90979926"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-netweaver"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) przy użyciu oprogramowania SAP NetWeaver

W tym samouczku dowiesz się, jak zintegrować oprogramowanie SAP NetWeaver z usługą Azure Active Directory (Azure AD). Po zintegrowaniu integracji oprogramowania SAP NetWeaver z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do rozwiązania SAP NetWeaver.
* Zezwól użytkownikom na automatyczne logowanie do oprogramowania SAP NetWeaver przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) w systemie SAP NetWeaver.
* Oprogramowanie SAP NetWeaver w wersji co najmniej 7.20

## <a name="scenario-description"></a>Opis scenariusza

* Rozwiązanie SAP NetWeaver obsługuje zarówno protokół **SAML** (**SSO zainicjowany przez usługę Sp**), jak i **OAuth**. W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym. 

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

> [!NOTE]
> Skonfiguruj aplikację w języku SAML lub OAuth zgodnie z wymaganiami organizacji. 

## <a name="adding-sap-netweaver-from-the-gallery"></a>Dodawanie oprogramowania SAP NetWeaver z galerii

Aby skonfigurować integrację oprogramowania SAP NetWeaver z usługą Azure AD, należy dodać oprogramowanie SAP NetWeaver z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **SAP NetWeaver** w polu wyszukiwania.
1. Wybierz pozycję **SAP NetWeaver** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-sap-netweaver"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla oprogramowania SAP NetWeaver

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą oprogramowania SAP NetWeaver przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w oprogramowaniu SAP NetWeaver.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą oprogramowania SAP NetWeaver, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** , aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj rozwiązanie SAP NetWeaver przy użyciu protokołu SAML](#configure-sap-netweaver-using-saml)** , aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz użytkownika testowego SAP NetWeaver](#create-sap-netweaver-test-user)** , aby miał odpowiednik B. Simon w oprogramowaniu SAP NetWeaver, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** , aby sprawdzić, czy konfiguracja działa.
1. **[Skonfiguruj rozwiązanie SAP NetWeaver na potrzeby uwierzytelniania OAuth](#configure-sap-netweaver-for-oauth)** , aby skonfigurować ustawienia protokołu OAuth po stronie aplikacji.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w oprogramowaniu SAP NetWeaver, wykonaj następujące kroki:

1. Otwórz nowe okno przeglądarki sieci Web i zaloguj się w witrynie firmy SAP NetWeaver jako administrator

1. Upewnij się, że usługi **http** i **https** są aktywne i że w kodzie transakcji **SMICM** są przypisane odpowiednie porty.

1. Zaloguj się do klienta biznesowego systemu SAP system (T01), w którym jest wymagane logowanie jednokrotne i aktywuj zarządzanie sesjami zabezpieczeń protokołu HTTP.

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

    b. W razie potrzeby skoryguj parametry w profilu wystąpienie/domyślny system SAP i uruchom ponownie system SAP.

    c. Kliknij dwukrotnie odpowiedni klient, aby włączyć sesję zabezpieczeń protokołu HTTP.

    ![Sesja zabezpieczeń protokołu HTTP ](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_profileparameter.png)

    d. Aktywuj poniższe usługi SICF:
    ```
    /sap/public/bc/sec/saml2
    /sap/public/bc/sec/cdc_ext_service
    /sap/bc/webdynpro/sap/saml2
    /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
    ```
1. Przejdź do kodu transakcji **SAML2** w kliencie biznesowym systemu SAP [T01/122]. Spowoduje to otwarcie interfejsu użytkownika w przeglądarce. W tym przykładzie dla klienta biznesowego systemu SAP przyjmujemy wartość 122.

    ![Kod transakcji](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_sapbusinessclient.png)

1. Podaj nazwę użytkownika i hasło, które należy wprowadzić w interfejsie użytkownika, a następnie kliknij pozycję **Edit** (Edytuj).

    ![Nazwa użytkownika i hasło](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_userpwd.png)

1. W polu **Provider Name** (Nazwa dostawcy) zastąp ciąg T01122 ciągiem `http://T01122` i kliknij pozycję **Save** (Zapisz).

    > [!NOTE]
    > Domyślna nazwa dostawcy jest uznawana za `<sid><client>` Format, ale usługa Azure AD oczekuje nazwy w formacie `<protocol>://<name>` , zaleca się, aby zachować nazwę dostawcy, tak `https://<sid><client>` aby zezwalała na konfigurowanie wielu aparatów ABAP SAP NetWeaver w usłudze Azure AD.

    ![Wiele aparatów ABAP oprogramowania SAP NetWeaver](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_providername.png)

1. **Generowanie metadanych dostawcy usługi** — po skonfigurowaniu ustawień **dostawcy lokalnego** i **zaufanych dostawców** w interfejsie użytkownika SAML 2.0 następnym krokiem będzie wygenerowanie pliku metadanych dostawcy usług (który zawiera wszystkie ustawienia, kontekst uwierzytelniania i inne konfiguracje w systemie SAP). Gdy plik zostanie wygenerowany, należy przekazać go w usłudze Azure AD.

    ![Generowanie metadanych dostawcy usług](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_generatesp.png)

    a. Przejdź do **karty dostawcy lokalnego**.

    b. Kliknij pozycję **Metadata** (Metadane).

    c. Zapisz wygenerowany **plik XML metadanych** na komputerze i przekaż go w sekcji **Podstawowa konfiguracja SAML** , aby automatycznie wypełnić wartości **identyfikatorów** i **adresów URL odpowiedzi** w Azure Portal.

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **SAP NetWeaver** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** kliknij ikonę Edytuj/pióra, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wykonaj następujące czynności:

    a. Kliknij przycisk **Przekaż plik metadanych** , aby przekazać **plik metadanych dostawcy usług**, który został uzyskany wcześniej.

    b. Kliknij **logo folderu**, aby wybrać plik metadanych, a następnie kliknij pozycję **Przekaż**.

    c. Po pomyślnym przekazaniu pliku metadanych wartości **Identyfikator** i **Adres URL odpowiedzi** zostaną automatycznie wypełnione w polach tekstowych sekcji **Podstawowa konfiguracja protokołu SAML**, jak pokazano poniżej:

    d. W polu tekstowym **adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<your company instance of SAP NetWeaver>`

    > [!NOTE]
    > Zaobserwowaliśmy, że niektórzy klienci zgłaszają błąd polegający na tym, że adres URL odpowiedzi skonfigurowany dla ich wystąpienia jest nieprawidłowy. Jeśli wystąpi taki błąd, możesz użyć następującego skryptu programu PowerShell jako obejścia w celu ustawienia prawidłowego adresu URL odpowiedzi dla wystąpienia:
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > Identyfikator obiektu ServicePrincipal musisz najpierw ustawić samodzielnie lub możesz również przekazać go w tym miejscu.

1. Aplikacja SAP NetWeaver oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe Atrybuty użytkownika.

    ![Edytuj atrybutów](common/edit-attribute.png)

1. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej, i wykonaj następujące czynności:

    a. Kliknij **ikonę Edytuj**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![ikona edycji](./media/sapnetweaver-tutorial/nameidattribute.png)

    ![image (obraz)](./media/sapnetweaver-tutorial/nameidattribute1.png)

    b. Z listy **Przekształcenie** wybierz pozycję **ExtractMailPrefix()**.

    c. Z listy **parametr 1** wybierz pozycję **User. userPrincipalName**.

    d. Kliknij przycisk **Zapisz**.

1. Na stronie **Konfiguruj pojedyncze Sign-On za pomocą elementu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

   ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie rozwiązania SAP NetWeaver** skopiuj odpowiednie adresy URL na podstawie wymagań.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
    1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
    1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
    1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
    1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do rozwiązania SAP NetWeaver.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **SAP NetWeaver**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-sap-netweaver-using-saml"></a>Konfigurowanie rozwiązania SAP NetWeaver przy użyciu protokołu SAML

1. Zaloguj się do systemu SAP i przejdź do kodu transakcji SAML2. Spowoduje to otwarcie nowego okna przeglądarki z ekranem konfiguracji protokołu SAML.

2. Aby skonfigurować punkty końcowe dla zaufanego dostawcy tożsamości (Azure AD), przejdź na kartę **Trusted Providers** (Zaufani dostawcy).

    ![Konfigurowanie jednego Sign-On zaufanych dostawców](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_samlconfig.png)

3. Naciśnij pozycję **Add** (Dodaj) i wybierz polecenie **Upload Metadata File** (Przekaż plik metadanych) z menu kontekstowego.

    ![Skonfiguruj pojedynczy Sign-On 2](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_uploadmetadata.png)

4. Przekaż plik metadanych, który został pobrany z witryny Azure Portal.

    ![Skonfiguruj pojedynczy Sign-On 3](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_metadatafile.png)

5. Na następnym ekranie wpisz nazwę aliasu. Na przykład aadsts i naciśnij przycisk **dalej** , aby kontynuować.

    ![Skonfiguruj pojedynczy Sign-On 4](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_aliasname.png)

6. Upewnij się, że w polu **Digest Algorithm** (Algorytm porządkowania ) znajduje się wartość **SHA-256** i że nie są wymagane żadne zmiany, a następnie naciśnij pozycję **Next** (Dalej).

    ![Skonfiguruj pojedynczy Sign-On 5](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_identityprovider.png)

7. Na stronie **Single Sign-On Endpoints** (Punkty końcowe logowania jednokrotnego) użyj opcji **HTTP POST** i kliknij przycisk **Next** (Dalej), aby kontynuować.

    ![Skonfiguruj pojedynczy Sign-On 6](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect.png)

8. Na stronie **Single Logout Endpoints** (Punkty końcowe wylogowania jednokrotnego) wybierz opcję **HTTPRedirect** i kliknij przycisk **Next** (Dalej), aby kontynuować.

    ![Skonfiguruj pojedynczy Sign-On 7](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect1.png)

9. Na stronie **Artifact Endpoints** (Punkty końcowe artefaktu) naciśnij przycisk **Next** (Dalej), aby kontynuować.

    ![Skonfiguruj pojedynczy Sign-On 8](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_artifactendpoint.png)

10. Na stronie **Authentication Requirements** (Wymagania dotyczące uwierzytelniania) kliknij przycisk **Finish** (Zakończ).

    ![Konfigurowanie pojedynczego Sign-On 9](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_authentication.png)

11. Przejdź do pozycji karta Federacja **zaufanych dostawców**  >  **tożsamość** (od dołu ekranu). Kliknij pozycję **Edytuj**.

    ![Skonfiguruj pojedynczy Sign-On 10](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_trustedprovider.png)

12. Kliknij przycisk **Add** (Dodaj) na karcie **Identity Federation** (Federacja tożsamości) (dolne okno).

    ![Skonfiguruj pojedynczy Sign-On 11](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_addidentityprovider.png)

13. W oknie podręcznym wybierz opcję **nieokreślone** w **obsługiwanych formatach NameID** , a następnie kliknij przycisk OK.

    ![Skonfiguruj pojedynczy Sign-On 12](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameid.png)

14. Należy pamiętać, że wartości w polu **Źródło identyfikatora użytkownika** i **Mapowanie identyfikatora użytkownika** określają łącze między użytkownikiem SAP a usługą Azure AD.  

    #### <a name="scenario-sap-user-to-azure-ad-user-mapping"></a>Scenariusz: użytkownik SAP do mapowania użytkowników usługi Azure AD.

    a. Zrzut ekranu przedstawiający szczegóły identyfikatora nazwy w systemie SAP.

    ![Skonfiguruj pojedynczy Sign-On 13](./media/sapnetweaver-tutorial/nameiddetails.png)

    b. Zrzut ekranu przedstawiający wymagane oświadczenia z usługi Azure AD.

    ![Skonfiguruj pojedynczy Sign-On 14](./media/sapnetweaver-tutorial/claimsaad1.png)

    #### <a name="scenario-select-sap-user-id-based-on-configured-email-address-in-su01-in-this-case-email-id-should-be-configured-in-su01-for-each-user-who-requires-sso"></a>Scenariusz: wybierz pozycję Identyfikator użytkownika SAP na podstawie skonfigurowanego adresu e-mail w SU01. W tym przypadku Identyfikator poczty e-mail powinien być skonfigurowany w usłudze su01 dla każdego użytkownika, który wymaga logowania jednokrotnego.

    a.  Zrzut ekranu przedstawiający szczegóły identyfikatora nazwy w systemie SAP.

    ![Skonfiguruj pojedynczy Sign-On 15](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameiddetails1.png)

    b. Zrzut ekranu przedstawiający wymagane oświadczenia z usługi Azure AD.

    ![Skonfiguruj pojedynczy Sign-On 16](./media/sapnetweaver-tutorial/claimsaad2.png)

15. Kliknij przycisk **Zapisz**, a następnie kliknij przycisk **Włącz**, aby włączyć dostawcę tożsamości.

    ![Konfigurowanie jednego Sign-On 17](./media/sapnetweaver-tutorial/configuration1.png)

16. Po wyświetleniu monitu kliknij przycisk **OK**.

    ![Skonfiguruj pojedynczy Sign-On 18](./media/sapnetweaver-tutorial/configuration2.png)

    ### <a name="create-sap-netweaver-test-user"></a>Tworzenie użytkownika testowego oprogramowania SAP NetWeaver

    W tej sekcji utworzysz użytkownika o nazwie B. Simon w oprogramowaniu SAP NetWeaver. Aby dodać użytkownika na platformie SAP NetWeaver, skontaktuj się z zespołem ekspertów ds. systemu SAP w swojej organizacji lub z odpowiednim partnerem firmy SAP.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

1. Po aktywowaniu usługi Azure AD jako dostawcy tożsamości spróbuj uzyskać dostęp do poniższego adresu URL, aby sprawdzić działanie logowania jednokrotnego (nie zostanie wyświetlony monit o nazwę użytkownika i hasło)

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    (lub) użyj poniższego adresu URL

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    > [!NOTE]
    > Zastąp ciąg „sapurl” faktyczną nazwą hosta systemu SAP.

2. Powyższy adres URL powinien przenieść Cię na pokazany poniżej ekran. Jeśli poniższa strona zostanie wyświetlona, oznacza to, że konfiguracja logowania jednokrotnego usługi Azure AD została przeprowadzona pomyślnie.

    ![Testuj pojedyncze Sign-On](./media/sapnetweaver-tutorial/testingsso.png)

3. Jeśli zostanie wyświetlony monit o nazwę użytkownika i hasło, zdiagnozuj problem, włączając śledzenie przy użyciu poniższego adresu URL

    `https://<sapurl>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#`

## <a name="configure-sap-netweaver-for-oauth"></a>Konfigurowanie oprogramowania SAP NetWeaver na potrzeby uwierzytelniania OAuth

1. Udokumentowany proces SAP jest dostępny w lokalizacji: [Usługa bramy NetWeaver Włączanie i uwierzytelnianie OAuth 2,0](https://wiki.scn.sap.com/wiki/display/Security/NetWeaver+Gateway+Service+Enabling+and+OAuth+2.0+Scope+Creation)

2. Przejdź do SPRO i Znajdź pozycję **Aktywuj i obsługuj usługi**.

    ![Aktywowanie i konserwowanie usług](./media/sapnetweaver-tutorial/oauth01.png)

3. W tym przykładzie chcemy połączyć usługę OData: `DAAG_MNGGRP` z uwierzytelnianiem OAuth na potrzeby rejestracji jednokrotnej usługi Azure AD. Użyj nazwy usługi technicznej Wyszukaj usługę `DAAG_MNGGRP` i aktywuj ją, jeśli jeszcze nie jest aktywna, lub Wyszukaj stan na `green` karcie węzły zapory ICF). Upewnij się, że alias systemowy (podłączony system wewnętrznej bazy danych, gdzie usługa jest rzeczywiście uruchomiona) jest poprawny.

    ![Usługa OData](./media/sapnetweaver-tutorial/oauth02.png)

    * Następnie kliknij pozycję przycisk **uwierzytelniania OAuth** na górnym pasku przycisków i przypisz `scope` (Zachowaj nazwę domyślną jako oferowaną).

4. W naszym przykładzie zakres jest `DAAG_MNGGRP_001` generowany na podstawie nazwy usługi przez automatyczne dodanie numeru. `/IWFND/R_OAUTH_SCOPES`Można użyć raportu, aby zmienić nazwę zakresu lub utworzyć ręcznie.

    ![Konfigurowanie uwierzytelniania OAuth](./media/sapnetweaver-tutorial/oauth03.png)

    > [!NOTE]
    > Message `soft state status is not supported` — można zignorować, ponieważ nie ma problemu. Więcej informacji można znaleźć [tutaj](https://help.sap.com/doc/saphelp_nw74/7.4.16/1e/c60c33be784846aad62716b4a1df39/content.htm?no_cache=true)

### <a name="create-a-service-user-for-the-oauth-20-client"></a>Tworzenie użytkownika usługi dla klienta OAuth 2,0

1. OAuth2 używa elementu, `service ID` Aby uzyskać token dostępu dla użytkownika końcowego w jego imieniu. Ważne ograniczenie przez projektowanie protokołu OAuth: `OAuth 2.0 Client ID` musi być taka sama jak w przypadku `username` logowania przy użyciu klienta OAuth 2,0 podczas żądania tokenu dostępu. W związku z tym w naszym przykładzie będziemy rejestrować klienta protokołu OAuth 2,0 o nazwie KLIENT1, a w systemie SAP musi istnieć użytkownik o tej samej nazwie (KLIENT1), a użytkownik zostanie skonfigurowany do użycia przez określoną aplikację. 

2. Podczas rejestrowania klienta OAuth korzystamy z programu `SAML Bearer Grant type` .

    >[!NOTE]
    >Aby uzyskać więcej informacji, zapoznaj się z artykułem rejestracja klienta OAuth 2,0 dla typu dotacji elementu SAML w [tym miejscu](https://wiki.scn.sap.com/wiki/display/Security/OAuth+2.0+Client+Registration+for+the+SAML+Bearer+Grant+Type)

3. tcod: SU01/Create User KLIENT1 AS `System type` i Assign Password, Zapisz go jako wymagające podania poświadczeń do programisty interfejsu API, który powinien nagrywać przy użyciu nazwy użytkownika do kodu wywołującego. Nie należy przypisywać profilu ani roli.

### <a name="register-the-new-oauth-20-client-id-with-the-creation-wizard"></a>Rejestrowanie nowego identyfikatora klienta OAuth 2,0 przy użyciu Kreatora tworzenia

1. Aby zarejestrować nowy **SOAUTH2**Transaction **klienta OAuth 2,0** . Zostanie wyświetlona informacja o klientach OAuth 2,0, które zostały już zarejestrowane. Wybierz pozycję **Utwórz** , aby uruchomić kreatora dla nowego klienta OAuth o nazwie "CLIENT1" w tym przykładzie.

2. Przejdź do T-Code: **SOAUTH2** i podaj opis, a następnie kliknij przycisk **dalej**.

    ![SOAUTH2](./media/sapnetweaver-tutorial/oauth04.png)

    ![Identyfikator klienta OAuth 2,0](./media/sapnetweaver-tutorial/oauth05.png)

3. Wybierz już dodany **SAML2 dostawcy tożsamości — Azure AD** z listy rozwijanej i Zapisz.

    ![SAML2 dostawcy tożsamości — Azure AD 1](./media/sapnetweaver-tutorial/oauth06.png)

    ![SAML2 dostawcy tożsamości — Azure AD 2](./media/sapnetweaver-tutorial/oauth07.png)

    ![SAML2 dostawcy tożsamości — Azure AD 3](./media/sapnetweaver-tutorial/oauth08.png)

4. Kliknij pozycję **Dodaj** w obszarze przypisanie zakresu, aby dodać wcześniej utworzony zakres: `DAAG_MNGGRP_001`

    ![Zakres](./media/sapnetweaver-tutorial/oauth09.png)

    ![przypisanie zakresu](./media/sapnetweaver-tutorial/oauth10.png)

5. Kliknij przycisk **Zakończ**.

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu usługi Azure AD SAP NetWeaver można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)