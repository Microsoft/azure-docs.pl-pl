---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) za pomocą oprogramowania SAP Fiori | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i SAP Fiori.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/05/2019
ms.author: jeedes
ms.openlocfilehash: 35bb05eb019a1284a8a418df0197960130e8d013
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92897300"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-fiori"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) przy użyciu oprogramowania SAP Fiori

W tym samouczku dowiesz się, jak zintegrować oprogramowanie SAP Fiori z usługą Azure Active Directory (Azure AD). Po zintegrowaniu integracji oprogramowania SAP Fiori z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do rozwiązania SAP Fiori.
* Zezwól użytkownikom na automatyczne logowanie do oprogramowania SAP Fiori przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) w systemie SAP Fiori.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa SAP Fiori obsługuje logowanie jednokrotne w usłudze **SP**

> [!NOTE]
> W przypadku uwierzytelniania w trybie iFrame zainicjowanego przez oprogramowanie SAP Fiori zalecamy użycie parametru **Ispassing** w elemencie SAML AuthnRequest do uwierzytelniania dyskretnego. Aby uzyskać więcej szczegółowych informacji o parametrze **Ispassy** , zobacz informacje o rejestracji jednokrotnej [protokołu SAML usługi Azure AD](../develop/single-sign-on-saml-protocol.md)

## <a name="adding-sap-fiori-from-the-gallery"></a>Dodawanie rozwiązania SAP Fiori z galerii

Aby skonfigurować integrację oprogramowania SAP Fiori w usłudze Azure AD, musisz dodać do listy zarządzanych aplikacji SaaS SAP Fiori z galerii.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje** .
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja** .
1. W sekcji **Dodaj z galerii** wpisz **SAP Fiori** w polu wyszukiwania.
1. Wybierz pozycję **SAP Fiori** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-fiori"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla oprogramowania SAP Fiori

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą oprogramowania SAP Fiori przy użyciu użytkownika testowego o nazwie **B. Simon** . Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w oprogramowaniu SAP Fiori.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą oprogramowania SAP Fiori, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Konfigurowanie logowania JEDNOkrotnego SAP Fiori](#configure-sap-fiori-sso)** — w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego SAP Fiori](#create-sap-fiori-test-user)** , aby dysponować odpowiednikiem B. Simon w oprogramowaniu SAP Fiori, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. Otwórz nowe okno przeglądarki sieci Web i zaloguj się do witryny firmy SAP Fiori jako administrator.

1. Upewnij się, że usługi **http** i **https** są aktywne oraz że odpowiednie porty są przypisane do kodu transakcji **SMICM** .

1. Zaloguj się do klienta SAP Business Client for SAP system **T01** , gdzie jest wymagane logowanie jednokrotne. Następnie aktywuj zarządzanie sesjami zabezpieczeń protokołu HTTP.

    1. Przejdź do kodu transakcji **SICF_SESSIONS** . Wyświetlane są wszystkie odpowiednie parametry profilu z bieżącymi wartościami. Wyglądają podobnie jak w poniższym przykładzie:

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
        > Dostosuj parametry zgodnie z wymaganiami organizacji. Powyższe parametry są podawane tylko jako przykład.

    1. W razie potrzeby dostosuj parametry w profilu wystąpienia (domyślnego) systemu SAP i ponownie uruchom system SAP.

    1. Kliknij dwukrotnie odpowiedniego klienta, aby włączyć sesję zabezpieczeń protokołu HTTP.

        ![Bieżące wartości odpowiedniej strony parametrów profilu w oprogramowaniu SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-profileparameter.png)

    1. Aktywuj następujące usługi SICF:

        ```
        /sap/public/bc/sec/saml2
        /sap/public/bc/sec/cdc_ext_service
        /sap/bc/webdynpro/sap/saml2
        /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
        ```

1. Przejdź do kodu transakcji **SAML2** w kliencie biznesowym dla systemu SAP system [ **T01/122** ]. Interfejs użytkownika konfiguracji zostanie otwarty w nowym oknie przeglądarki. W tym przykładzie korzystamy z klienta biznesowego dla SAP system 122.

    ![Strona logowania programu SAP Fiori Business Client](./media/sapfiori-tutorial/tutorial-sapnetweaver-sapbusinessclient.png)

1. Wprowadź nazwę użytkownika i hasło, a następnie wybierz pozycję **Zaloguj** się.

    ![Konfiguracja SAML 2,0 strony ABAP system T01/122 w oprogramowaniu SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-userpwd.png)

1. W polu **Nazwa dostawcy** Zastąp wartość **T01122** nazwą **http: \/ /T01122** , a następnie wybierz pozycję **Zapisz** .

    > [!NOTE]
    > Domyślnie nazwa dostawcy ma format \<sid> \<client> . Usługa Azure AD oczekuje nazwy w formacie \<protocol> :// \<name> . Zalecamy zachowanie nazwy dostawcy jako protokołu HTTPS \: // \<sid> \<client> , aby można było skonfigurować wiele aparatów ABAP SAP Fiori w usłudze Azure AD.

    ![Zaktualizowana nazwa dostawcy w konfiguracji SAML 2,0 strony ABAP system T01/122 w oprogramowaniu SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-providername.png)

1. Wybierz pozycję metadane **karty dostawcy lokalnego**  >  **Metadata** .

1. W oknie dialogowym **metadane SAML 2,0** Pobierz WYGENEROWANY plik XML metadanych i Zapisz go na komputerze.

    ![Link pobierania metadanych w oknie dialogowym metadanych SAP SAML 2,0](./media/sapfiori-tutorial/tutorial-sapnetweaver-generatesp.png)

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **SAP Fiori** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne** .
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML** .
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML** , jeśli masz **plik metadanych dostawcy usługi** , wykonaj następujące kroki:

    a. Kliknij pozycję **Przekaż plik metadanych** .

    ![Przekazywanie pliku metadanych](common/upload-metadata.png)

    b. Kliknij **logo folderu** , aby wybrać plik metadanych, a następnie kliknij pozycję **Przekaż** .

    ![wybierz plik metadanych](common/browse-upload-metadata.png)

    c. Po pomyślnym przekazaniu pliku metadanych wartości **identyfikatorów** i **adresów URL odpowiedzi** są wypełniane automatycznie w okienku **podstawowe konfiguracje języka SAML** . W polu **adres URL logowania** wprowadź adres URL, który ma następujący wzorzec: `https:\//\<your company instance of SAP Fiori\>` .

    > [!NOTE]
    > Kilku klientów zgłasza błędy związane z niepoprawnie skonfigurowanymi wartościami **adresu URL odpowiedzi** . Jeśli ten błąd jest wyświetlany, można użyć następującego skryptu programu PowerShell, aby ustawić prawidłowy adres URL odpowiedzi dla danego wystąpienia:
    >
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > 
    > Identyfikator obiektu można ustawić `ServicePrincipal` samodzielnie przed uruchomieniem skryptu lub przekazać go tutaj.

1. Aplikacja SAP Fiori oczekuje, że potwierdzenia SAML mają być w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Aby zarządzać tymi wartościami atrybutów, w okienku **Konfiguracja jednego Sign-On za pomocą SAML** wybierz pozycję **Edytuj** .

    ![Okienko atrybuty użytkownika](common/edit-attribute.png)

1. W okienku **atrybuty użytkownika & oświadczenia** skonfiguruj atrybuty tokenu SAML, jak pokazano na powyższym obrazie. Następnie wykonaj następujące kroki:

    1. Wybierz pozycję **Edytuj** , aby otworzyć okienko **Zarządzanie oświadczeniami użytkowników** .

    1. Na liście **transformacja** wybierz pozycję **ExtractMailPrefix ()** .

    1. Na liście **parametr 1** wybierz pozycję **User. userPrincipalName** .

    1. Wybierz pozycję **Zapisz** .

       ![Okienko Zarządzaj oświadczeniami użytkowników](./media/sapfiori-tutorial/nameidattribute.png)

       ![Sekcja przekształcenia w okienku Zarządzaj oświadczeniami użytkowników](./media/sapfiori-tutorial/nameidattribute1.png)
    
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie rozwiązania SAP Fiori** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory** , wybierz pozycję **Użytkownicy** , a następnie wybierz pozycję **Wszyscy użytkownicy** .
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło** .
   1. Kliknij pozycję **Utwórz** .

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do rozwiązania SAP Fiori.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw** , a następnie wybierz pozycję **wszystkie aplikacje** .
1. Na liście Aplikacje wybierz pozycję **SAP Fiori** .
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy** .

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika** , a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-sap-fiori-sso"></a>Konfigurowanie logowania jednokrotnego SAP Fiori

1. Zaloguj się do systemu SAP i przejdź do kodu transakcji **SAML2** . Zostanie otwarte nowe okno przeglądarki ze stroną konfiguracji protokołu SAML.

1. Aby skonfigurować punkty końcowe dla zaufanego dostawcy tożsamości (Azure AD), wybierz kartę **Zaufani dostawcy** .

    ![Karta Zaufani dostawcy w oprogramowaniu SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-samlconfig.png)

1. Wybierz pozycję **Dodaj** , a następnie wybierz pozycję **Przekaż plik metadanych** z menu kontekstowego.

    ![Opcje dodawania i przekazywania metadanych plików w oprogramowaniu SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-uploadmetadata.png)

1. Przekaż plik metadanych, który został pobrany w Azure Portal. Wybierz pozycję **Dalej** .

    ![Wybierz plik metadanych do przekazania w oprogramowaniu SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-metadatafile.png)

1. Na następnej stronie w polu **alias** wprowadź nazwę aliasu. Na przykład **aadsts** . Wybierz pozycję **Dalej** .

    ![Pole alias w oprogramowaniu SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-aliasname.png)

1. Upewnij się, że wartość w polu **algorytm podsumowania** to **SHA-256** . Wybierz pozycję **Dalej** .

    ![Sprawdź wartość algorytmu digest w oprogramowaniu SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-identityprovider.png)

1. W obszarze **punkty końcowe pojedynczej Sign-On** wybierz pozycję **http post** , a następnie wybierz przycisk **dalej** .

    ![Opcje pojedynczych punktów końcowych Sign-On w oprogramowaniu SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect.png)

1. W obszarze **punkty końcowe logowania jednokrotnego** wybierz pozycję **przekierowywanie http** , a następnie wybierz przycisk **dalej** .

    ![Opcje punktów końcowych wylogowania jednokrotnego w oprogramowaniu SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect1.png)

1. W obszarze **punkty końcowe artefaktu** wybierz pozycję **dalej** , aby kontynuować.

    ![Opcje punktów końcowych artefaktów w oprogramowaniu SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-artifactendpoint.png)

1. W obszarze **wymagania uwierzytelniania** wybierz pozycję **Zakończ** .

    ![Opcje wymagań uwierzytelniania i opcja zakończenia w oprogramowaniu SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-authentication.png)

1. Wybierz opcję Federacja tożsamości **dostawcy zaufanego**  >  **Identity Federation** (w dolnej części strony). Wybierz pozycję **Edytuj** .

    ![Karty zaufanych dostawców i tożsamości w oprogramowaniu SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-trustedprovider.png)

1. Wybierz pozycję **Dodaj** .

    ![Opcja Dodaj na karcie federacji tożsamości](./media/sapfiori-tutorial/tutorial-sapnetweaver-addidentityprovider.png)

1. W oknie dialogowym **obsługiwane formaty NameID** wybierz opcję **nieokreślony** . Wybierz przycisk **OK** .

    ![Okno dialogowe obsługiwane formaty NameID i opcje w oprogramowaniu SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameid.png)

    Wartości dla opcji **Źródło identyfikatora użytkownika** i **Mapowanie identyfikatora użytkownika** określają łącze między użytkownikiem SAP a roszczeń usługi Azure AD.  

    **Scenariusz 1** : mapowanie użytkownika SAP do usługi Azure AD

    1. W oprogramowaniu SAP w obszarze **Szczegóły formatu NameID "unnieokreślone"** Zwróć uwagę na szczegóły:

        ![Zrzut ekranu przedstawiający okno dialogowe "Szczegóły NameID formatowania" nieokreślony "" w S A P.](./media/sapfiori-tutorial/nameiddetails.png)

    1. W Azure Portal w obszarze **atrybuty użytkownika & oświadczenia** Zanotuj wymagane oświadczenia z usługi Azure AD.

        ![Zrzut ekranu przedstawiający okno dialogowe "atrybuty użytkownika & oświadczenia".](./media/sapfiori-tutorial/claimsaad1.png)

    **Scenariusz 2** : Wybierz identyfikator użytkownika SAP na podstawie skonfigurowanego adresu E-mail w SU01. W takim przypadku Identyfikator poczty e-mail powinien być skonfigurowany w SU01 dla każdego użytkownika, który wymaga logowania jednokrotnego.

    1.  W oprogramowaniu SAP w obszarze **Szczegóły formatu NameID "unnieokreślone"** Zwróć uwagę na szczegóły:

        ![Szczegóły formatu NameID w oknie dialogowym "nieokreślone" w oprogramowaniu SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameiddetails1.png)

    1. W Azure Portal w obszarze **atrybuty użytkownika & oświadczenia** Zanotuj wymagane oświadczenia z usługi Azure AD.

       ![Okno dialogowe atrybuty użytkownika & oświadczenia w Azure Portal](./media/sapfiori-tutorial/claimsaad2.png)

1. Wybierz pozycję **Zapisz** , a następnie wybierz pozycję **Włącz** , aby włączyć dostawcę tożsamości.

    ![Opcje Zapisz i Włącz w oprogramowaniu SAP](./media/sapfiori-tutorial/configuration1.png)

1. Po wyświetleniu monitu wybierz **przycisk OK** .

    ![Opcja OK w oknie dialogowym Konfiguracja SAML 2,0 w oprogramowaniu SAP](./media/sapfiori-tutorial/configuration2.png)

### <a name="create-sap-fiori-test-user"></a>Utwórz użytkownika testowego SAP Fiori

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w oprogramowaniu SAP Fiori. Współpracuj z własnym zespołem SAP lub partnerem SAP organizacji, aby dodać użytkownika na platformie SAP Fiori.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

1. Po aktywowaniu usługi Azure AD dostawcy tożsamości w programie SAP Fiori spróbuj uzyskać dostęp do jednego z następujących adresów URL w celu przetestowania logowania jednokrotnego (nie należy monitować o podanie nazwy użytkownika i hasła):

    * https: \/ / \<sapurl\> /SAP/BC/BSP/SAP/it00/default.htm
    * https: \/ / \<sapurl\> /SAP/BC/BSP/SAP/it00/default.htm

    > [!NOTE]
    > Zamień *sapurl* na rzeczywistą nazwę hosta SAP.

1. Adres URL testu powinien wykonać Cię na poniższej stronie testowej aplikacji w oprogramowaniu SAP. Jeśli zostanie otwarta strona, logowanie jednokrotne w usłudze Azure AD zostało pomyślnie skonfigurowane.

    ![Strona standardowej aplikacji testowej w oprogramowaniu SAP](./media/sapfiori-tutorial/testingsso.png)

1. Jeśli zostanie wyświetlony monit o podanie nazwy użytkownika i hasła, Włącz śledzenie, aby ułatwić zdiagnozowanie problemu. Użyj następującego adresu URL śledzenia: https: \/ / \<sapurl\> /SAP/BC/WebDynpro/SAP/sec_diag_tool? SAP-Client = 122&SAP-language = EN #.

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj rozwiązanie SAP Fiori w usłudze Azure AD](https://aad.portal.azure.com/)