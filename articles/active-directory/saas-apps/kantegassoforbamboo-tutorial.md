---
title: 'Samouczek: integracja Azure Active Directory z logowaniem jednokrotnym w usłudze Kantega dla Bamboo | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Kantega Logowanie jednokrotne dla usługi Bamboo.
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
ms.openlocfilehash: 5b1034454bbdee32b33222321ded2421b83bfd0d
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91850782"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-bamboo"></a>Samouczek: integracja Azure Active Directory z logowaniem jednokrotnym w usłudze Kantega dla Bamboo

W ramach tego samouczka nauczysz się zintegrować usługę Kantega SSO for Bamboo z usługą Azure Active Directory (Azure AD).
Integrowanie logowania jednokrotnego w usłudze Kantega z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Kantega SSO dla Bamboo.
* Możesz włączyć automatyczne logowanie użytkowników, aby Kantega Logowanie jednokrotne w usłudze Bamboo (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z logowaniem jednokrotnym w usłudze Kantega dla Bamboo, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Kantega Logowanie jednokrotne dla subskrypcji usługi Bamboo z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Logowanie jednokrotne w usłudze Kantega dla usługi Bamboo obsługuje usługę **SP i dostawcy tożsamości** zainicjowana

## <a name="adding-kantega-sso-for-bamboo-from-the-gallery"></a>Dodawanie Kantega SSO dla Bamboo z galerii

Aby skonfigurować integrację logowania jednokrotnego Kantega dla Bamboo w usłudze Azure AD, musisz dodać Kantega SSO dla Bamboo z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Kantega SSO dla Bamboo z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **KANTEGA SSO dla Bamboo**, wybierz pozycję **Kantega SSO dla Bamboo** z poziomu panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![Kantega Logowanie jednokrotne dla Bamboo na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą logowania jednokrotnego w usłudze Kantega dla Bamboo na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze Kantega SSO dla Bamboo.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą logowania jednokrotnego w usłudze Kantega dla Bamboo, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie JEDNOkrotne w usłudze Kantega](#configure-kantega-sso-for-bamboo-single-sign-on)** , aby móc skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz Kantega Logowanie jednokrotne dla użytkownika testowego](#create-kantega-sso-for-bamboo-test-user)** , aby uzyskać odpowiednik Britta Simon w Kantega SSO dla Bamboo, który jest połączony z reprezentacją usługi Azure AD użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą logowania jednokrotnego w usłudze Kantega dla Bamboo, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Kantega SSO dla Bamboo** wybierz pozycję **Logowanie**jednokrotne.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia podstawową konfigurację języka SAML, w której można wprowadzić identyfikator, odpowiedź U R L i wybrać pozycję Zapisz.](common/idp-intiated.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    ![Zrzut ekranu przedstawia ustawienie dodatkowego U R LS, gdzie można wprowadzić znak U R L.](common/metadata-upload-additional-signon.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Te wartości są odbierane podczas konfiguracji wtyczki Bamboo, która została omówiona w dalszej części samouczka.

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

7. W sekcji **Konfigurowanie KANTEGA SSO dla Bamboo** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-kantega-sso-for-bamboo-single-sign-on"></a>Konfigurowanie logowania jednokrotnego Kantega dla Bamboo pojedynczej Sign-On

1. W innym oknie przeglądarki sieci Web Zaloguj się na serwerze lokalnym Bamboo jako administrator.

1. Zatrzymaj wskaźnik myszy nad ikoną koła zębatego i kliknij pozycję **Dodatki**.

    ![Zrzut ekranu przedstawia Dodatki wybrane z menu Ustawienia.](./media/kantegassoforbamboo-tutorial/addon1.png)

1. W sekcji karty Dodatki kliknij pozycję **Znajdź nowe dodatki**. Wyszukaj **Kantega Logowanie jednokrotne dla Bamboo (SAML & Kerberos)** i kliknij przycisk **Instaluj** , aby zainstalować nową wtyczkę SAML.

    ![Zrzut ekranu przedstawia administrację Bamboo z Kantega S S dla Bamboo.](./media/kantegassoforbamboo-tutorial/addon2.png)

1. Rozpocznie się instalacja wtyczki.

    ![Zrzut ekranu przedstawia postęp instalacji dla Kantega S S dla Bamboo.](./media/kantegassoforbamboo-tutorial/addon21.png)

1. Po zakończeniu instalacji. Kliknij przycisk **Zamknij**.

    ![Zrzut ekranu przedstawia przycisk Zamknij.](./media/kantegassoforbamboo-tutorial/addon33.png)

1. Kliknij pozycję **Zarządzaj**.

    ![Zrzut ekranu przedstawia przycisk Zarządzaj.](./media/kantegassoforbamboo-tutorial/addon34.png)

1. Kliknij pozycję **Configure** (Konfiguruj), aby skonfigurować nową wtyczkę.

    ![Zrzut ekranu przedstawia dodatki zainstalowane przez użytkownika z wybraną pozycją Konfiguruj.](./media/kantegassoforbamboo-tutorial/addon3.png)

1. W sekcji **SAML** . Wybierz pozycję **Azure Active Directory (Azure AD)** z listy rozwijanej **Dodaj dostawcę tożsamości** .

    ![Zrzut ekranu przedstawia Kantega pojedyncze Sign-On z platformą Azure A D wybraną jako dostawca tożsamości.](./media/kantegassoforbamboo-tutorial/addon4.png)

1. Wybierz poziom subskrypcji jako **podstawowy**.

    ![Zrzut ekranu przedstawia przygotowanie platformy Azure A D z zaznaczoną opcją podstawowa.](./media/kantegassoforbamboo-tutorial/addon5.png)

1. W sekcji **właściwości aplikacji** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia sekcję właściwości aplikacji, w której można podać informacje w tym kroku.](./media/kantegassoforbamboo-tutorial/addon6.png)

    a. Skopiuj wartość **Identyfikator URI identyfikatora aplikacji** i użyj jej jako **identyfikatora, adresu URL odpowiedzi i adresu URL Sign-On** w sekcji **podstawowa konfiguracja SAML** w programie Azure Portal.

    b. Kliknij przycisk **Dalej**.

1. W sekcji **Importowanie metadanych** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia sekcję Importowanie metadanych, w której można przejść do pliku metadanych.](./media/kantegassoforbamboo-tutorial/addon7.png)

    a. Wybierz pozycję **plik metadanych na komputerze**i Przekaż plik metadanych, który został pobrany z Azure Portal.

    b. Kliknij przycisk **Dalej**.

1. W sekcji **Nazwa i lokalizacja logowania jednokrotnego** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia nazwę i lokalizację S, gdzie Azure A D jest nazwą dostawcy tożsamości.](./media/kantegassoforbamboo-tutorial/addon8.png)

    a. Dodaj nazwę dostawcy tożsamości w polu tekstowym **Nazwa dostawcy tożsamości** (np. Azure AD).

    b. Kliknij przycisk **Dalej**.

1. Sprawdź certyfikat podpisywania i kliknij przycisk **dalej**.

    ![Zrzut ekranu przedstawia weryfikację podpisu.](./media/kantegassoforbamboo-tutorial/addon9.png)

1. W sekcji **konta użytkowników Bamboo** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia konta użytkowników Bamboo, w których można utworzyć użytkowników.](./media/kantegassoforbamboo-tutorial/addon10.png)

    a. **W razie potrzeby wybierz opcję Utwórz użytkowników w katalogu wewnętrznym Bamboo** i wprowadź odpowiednią nazwę grupy dla użytkowników (może to być wiele nie. grup oddzielonych przecinkami).

    b. Kliknij przycisk **Dalej**.

1. Kliknij przycisk **Finish** (Zakończ).

    ![Zrzut ekranu przedstawia stronę podsumowania.](./media/kantegassoforbamboo-tutorial/addon11.png)

1. W sekcji **znane domeny dla usługi Azure AD** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia znane domeny dla systemu Azure A D, gdzie można wykonać te kroki.](./media/kantegassoforbamboo-tutorial/addon12.png)

    a. Wybierz opcję **znane domeny** w lewym panelu strony.

    b. Wprowadź nazwę domeny w polu tekstowym **znane domeny** .

    c. Kliknij przycisk **Zapisz**.

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

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji Britta Simon do korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do Kantega logowania jednokrotnego dla Bamboo.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **Kantega Logowanie jednokrotne dla Bamboo**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **KANTEGA SSO dla usługi Bamboo**.

    ![Link SSO Kantega dla usługi Bamboo na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-kantega-sso-for-bamboo-test-user"></a>Utwórz Kantega Logowanie jednokrotne dla użytkownika testowego Bamboo

Aby umożliwić użytkownikom usługi Azure AD logowanie się w usłudze Bamboo, muszą one być obsługiwane w usłudze Bamboo. W przypadku Kantega logowania jednokrotnego dla Bamboo, Inicjowanie obsługi jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do serwera lokalnego Bamboo jako administrator.

1. Zatrzymaj wskaźnik myszy nad ikoną koła zębatego i kliknij pozycję **User management** (Zarządzanie użytkownikami).

    ![Zrzut ekranu przedstawia zarządzanie użytkownikami wybrane z menu Ustawienia.](./media/kantegassoforbamboo-tutorial/user1.png)

1. Kliknij pozycję **Użytkownicy**. W sekcji **Dodawanie użytkownika** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia okienko Dodawanie użytkownika, w którym można wykonać te kroki.](./media/kantegassoforbamboo-tutorial/user2.png)

    a. W polu tekstowym **Username** (Nazwa użytkownika) wpisz adres e-mail użytkownika, na przykład Brittasimon@contoso.com.

    b. W polu tekstowym **Password** (Hasło) wpisz hasło użytkownika.

    c. W polu tekstowym **Potwierdź hasło** ponownie wprowadź hasło użytkownika.

    d. W polu tekstowym **Full Name** (Imię i nazwisko) wpisz imię i nazwisko użytkownika, np. Britta Simon.

    e. W polu tekstowym **Email** (Adres e-mail) wpisz adres e-mail użytkownika, np. Brittasimon@contoso.com.

    f. Kliknij przycisk **Zapisz**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka SSO Kantega dla Bamboo w panelu dostępu należy automatycznie zalogować się do usługi logowania jednokrotnego Kantega dla Bamboo, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
