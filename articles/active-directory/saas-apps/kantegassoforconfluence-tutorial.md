---
title: 'Samouczek: integracja Azure Active Directory z logowaniem jednokrotnym w usłudze Kantega dla Confluence | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Kantega Logowanie jednokrotne dla usługi Confluence.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: be86e04359c29696d208994d85d36b7740b60cc3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101646218"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-confluence"></a>Samouczek: integracja Azure Active Directory z logowaniem jednokrotnym w usłudze Kantega dla Confluence

W ramach tego samouczka nauczysz się zintegrować usługę Kantega SSO for Confluence z usługą Azure Active Directory (Azure AD).
Integrowanie logowania jednokrotnego w usłudze Kantega z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Kantega SSO dla Confluence.
* Możesz włączyć automatyczne logowanie użytkowników, aby Kantega Logowanie jednokrotne w usłudze Confluence (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z logowaniem jednokrotnym w usłudze Kantega dla Confluence, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Kantega Logowanie jednokrotne dla subskrypcji usługi Confluence z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Logowanie jednokrotne w usłudze Kantega dla usługi Confluence obsługuje usługę **SP i dostawcy tożsamości** zainicjowana

## <a name="adding-kantega-sso-for-confluence-from-the-gallery"></a>Dodawanie Kantega SSO dla Confluence z galerii

Aby skonfigurować integrację logowania jednokrotnego Kantega dla Confluence w usłudze Azure AD, musisz dodać Kantega SSO dla Confluence z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Kantega SSO dla Confluence z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **KANTEGA SSO dla Confluence**, wybierz pozycję **Kantega SSO dla Confluence** z poziomu panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![Kantega Logowanie jednokrotne dla Confluence na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą logowania jednokrotnego w usłudze Kantega dla Confluence na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze Kantega SSO dla Confluence.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą logowania jednokrotnego w usłudze Kantega dla Confluence, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie JEDNOkrotne w usłudze Kantega](#configure-kantega-sso-for-confluence-single-sign-on)** , aby móc skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz Kantega Logowanie jednokrotne dla użytkownika testowego](#create-kantega-sso-for-confluence-test-user)** , aby uzyskać odpowiednik Britta Simon w Kantega SSO dla Confluence, który jest połączony z reprezentacją usługi Azure AD użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą logowania jednokrotnego w usłudze Kantega dla Confluence, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Kantega SSO dla Confluence** wybierz pozycję **Logowanie** jednokrotne.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wykonaj następujące czynności:

    ![Zrzut ekranu pokazujący sekcję "podstawowa S A M" konfiguracji z wyróżnionymi polami "identifier" i "reply U R L" oraz wybranym przyciskiem "Zapisz".](common/idp-intiated.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    ![Kantega Logowanie jednokrotne dla domeny Confluence i adresów URL logowania jednokrotnego](common/metadata-upload-additional-signon.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Te wartości można uzyskać podczas konfigurowania wtyczki Confluence, co jest objaśnione w dalszej części tego samouczka.

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

7. W sekcji **Konfigurowanie KANTEGA SSO dla Confluence** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-kantega-sso-for-confluence-single-sign-on"></a>Konfigurowanie logowania jednokrotnego Kantega dla Confluence pojedynczej Sign-On

1. W innym oknie przeglądarki sieci Web Zaloguj się do **portalu administratora Confluence** jako administrator.

1. Zatrzymaj wskaźnik myszy nad ikoną koła zębatego i kliknij pozycję **Dodatki**.

    ![Zrzut ekranu pokazujący ikonę menu "koło zębate" i wybrane "Dodatki".](./media/kantegassoforconfluence-tutorial/addon1.png)

1. Na karcie **ATLASSIAN MARKETPLACE** (Platforma handlowa Atlassian) kliknij pozycję **Find new add-ons** (Znajdź nowe dodatki).

    ![Zrzut ekranu pokazujący kartę "ATTLASSIAN MARKETPLACE" z wybraną pozycją "Znajdź nowe dodatki".](./media/kantegassoforconfluence-tutorial/addon.png)

1. Wyszukaj **Kantega Logowanie jednokrotne dla Confluence protokołu SAML Kerberos** i kliknij przycisk **Instaluj** , aby zainstalować nową wtyczkę SAML.

    ![Zrzut ekranu przedstawiający stronę "Znajdź nowe dodatki" z opcją "Kantega S S for Confluence S A M L Kerberos" w polu wyszukiwania i wybranym przyciskiem "Zainstaluj".](./media/kantegassoforconfluence-tutorial/addon2.png)

1. Rozpocznie się instalacja wtyczki.

    ![Zrzut ekranu przedstawiający ekran wtyczki "Installing" (Instalowanie).](./media/kantegassoforconfluence-tutorial/addon3.png)

1. Po zakończeniu instalacji. Kliknij przycisk **Zamknij**.

    ![Zrzut ekranu przedstawiający ekran "zainstalowane i gotowe do użycia" z wybraną akcją "Zamknij".](./media/kantegassoforconfluence-tutorial/addon33.png)

1. Kliknij pozycję **Zarządzaj**.

    ![Zrzut ekranu, który pokazuje wtyczkę "Logowanie jednokrotne w usłudze Kantega with Kerberos i S L" z wybranym przyciskiem "Zarządzaj".](./media/kantegassoforconfluence-tutorial/addon34.png)

1. Kliknij pozycję **Configure** (Konfiguruj), aby skonfigurować nową wtyczkę.

    ![Zrzut ekranu przedstawiający stronę "Logowanie jednokrotne przy użyciu protokołu Kerberos i S-M" z wybranym przyciskiem "Konfiguruj".](./media/kantegassoforconfluence-tutorial/addon35.png)

1. Ten nowy dodatek plug-in można także znaleźć na karcie **USERS & SECURITY** (Użytkownicy i zabezpieczenia).

    ![Zrzut ekranu pokazujący kartę "Użytkownicy & zabezpieczenia" z wybraną akcją "Logowanie jednokrotne w Kantega".](./media/kantegassoforconfluence-tutorial/addon36.png)

1. W sekcji **SAML** . Wybierz pozycję **Azure Active Directory (Azure AD)** z listy rozwijanej **Dodaj dostawcę tożsamości** .

    ![Zrzut ekranu pokazujący sekcję "S A M" z wybraną pozycją "Dodaj dostawcę tożsamości" i "Azure Active Directory (Azure AD)".](./media/kantegassoforconfluence-tutorial/addon4.png)

1. Wybierz poziom subskrypcji jako **podstawowy**.

    ![Zrzut ekranu przedstawiający stronę "Przygotowywanie usługi Azure AD" z wybraną pozycją "podstawowa".](./media/kantegassoforconfluence-tutorial/addon5.png)

1. W sekcji **właściwości aplikacji** wykonaj następujące czynności:

    ![Zrzut ekranu pokazujący sekcję "właściwości aplikacji" z wyróżnionym polem "app and D U R L" i "Copy" (Kopiuj) i wybranym przyciskiem "dalej".](./media/kantegassoforconfluence-tutorial/addon6.png)

    a. Skopiuj wartość **Identyfikator URI identyfikatora aplikacji** i użyj jej jako **identyfikatora, adresu URL odpowiedzi i adresu URL Sign-On** w sekcji **podstawowa konfiguracja SAML** w programie Azure Portal.

    b. Kliknij przycisk **Dalej**.

1. W sekcji **Importowanie metadanych** wykonaj następujące czynności: 

    ![Zrzut ekranu przedstawiający sekcję "import metadanych" z wybraną pozycją "plik metadanych na moim komputerze".](./media/kantegassoforconfluence-tutorial/addon7.png)

    a. Wybierz pozycję **plik metadanych na komputerze** i Przekaż plik metadanych, który został pobrany z Azure Portal.

    b. Kliknij przycisk **Dalej**.

1. W sekcji **Nazwa i lokalizacja logowania jednokrotnego** wykonaj następujące czynności:

    ![Zrzut ekranu pokazujący lokalizację "Nazwa i S" z wyróżnioną polem tekstowym "Nazwa dostawcy tożsamości" i wybranym przyciskiem "dalej".](./media/kantegassoforconfluence-tutorial/addon8.png)

    a. Dodaj nazwę dostawcy tożsamości w polu tekstowym **Nazwa dostawcy tożsamości** (np. Azure AD).

    b. Kliknij przycisk **Dalej**.

1. Sprawdź certyfikat podpisywania i kliknij przycisk **dalej**.

    ![Zrzut ekranu przedstawiający sekcję "Weryfikacja podpisu" z wybranym przyciskiem "dalej".](./media/kantegassoforconfluence-tutorial/addon9.png)

1. W sekcji **konta użytkowników Confluence** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawiający sekcję "Confluence User Accounts" z zaznaczoną opcją "Utwórz użytkowników w katalogu wewnętrznym Confluence" i "dalej".](./media/kantegassoforconfluence-tutorial/addon10.png)

    a. **W razie potrzeby wybierz opcję Utwórz użytkowników w katalogu wewnętrznym Confluence** i wprowadź odpowiednią nazwę grupy dla użytkowników (może to być wiele nie. grup oddzielonych przecinkami).

    b. Kliknij przycisk **Dalej**.

1. Kliknij przycisk **Finish** (Zakończ).

    ![Zrzut ekranu przedstawiający stronę "Podsumowanie" z wybranym przyciskiem "Zakończ".](./media/kantegassoforconfluence-tutorial/addon11.png)

1. W sekcji **znane domeny dla usługi Azure AD** wykonaj następujące czynności: 

    ![Zrzut ekranu przedstawiający stronę "znane domeny dla usługi Azure AD" z wyróżnionym polem tekstowym "znane domeny" i wybranym przyciskiem "Zapisz".](./media/kantegassoforconfluence-tutorial/addon12.png)

    a. Wybierz opcję **znane domeny** w lewym panelu strony.

    b. Wprowadź nazwę domeny w polu tekstowym **znane domeny** .

    c. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz `brittasimon@yourcompanydomain.extension`  
    Na przykład BrittaSimon@contoso.com

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji Britta Simon do korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do Kantega logowania jednokrotnego dla Confluence.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **Kantega Logowanie jednokrotne dla Confluence**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **KANTEGA SSO dla usługi Confluence**.

    ![Link SSO Kantega dla usługi Confluence na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-kantega-sso-for-confluence-test-user"></a>Utwórz Kantega Logowanie jednokrotne dla użytkownika testowego Confluence

Aby umożliwić użytkownikom usługi Azure AD logowanie się w usłudze Confluence, muszą one być obsługiwane w usłudze Confluence. W przypadku Kantega logowania jednokrotnego dla Confluence, Inicjowanie obsługi jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do usługi logowania jednokrotnego w usłudze Kantega dla witryny firmy Confluence jako administrator.

1. Zatrzymaj wskaźnik myszy nad ikoną koła zębatego i kliknij pozycję **User management** (Zarządzanie użytkownikami).

    ![Zrzut ekranu pokazujący ikonę "koło zębate" i wybraną wartość "Zarządzanie użytkownikami".](./media/kantegassoforconfluence-tutorial/user1.png)

1. W obszarze Użytkownicy kliknij przycisk **Dodaj użytkowników** kartę. Na stronie **Dodawanie użytkownika** wykonaj następujące czynności:

    ![Dodawanie pracownika](./media/kantegassoforconfluence-tutorial/user2.png)

    a. W polu tekstowym **Username** (Nazwa użytkownika) wpisz adres e-mail użytkownika, na przykład Brittasimon@contoso.com.

    b. W polu tekstowym **Full Name** (Imię i nazwisko) wpisz imię i nazwisko użytkownika, np. Britta Simon.

    c. W polu tekstowym **Email** (Adres e-mail) wpisz adres e-mail użytkownika, np. Brittasimon@contoso.com.

    d. W polu tekstowym **hasło** wpisz hasło dla użytkownika.

    e. Kliknij pozycję **Confirm Password** (Potwierdź hasło), a następnie ponownie wprowadź hasło.

    f. Kliknij przycisk **Add** (Dodaj).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka SSO Kantega dla Confluence w panelu dostępu należy automatycznie zalogować się do usługi logowania jednokrotnego Kantega dla Confluence, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)