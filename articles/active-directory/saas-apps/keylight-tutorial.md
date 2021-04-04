---
title: 'Samouczek: integracja Azure Active Directory z programem LockPath Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i LockPath.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.openlocfilehash: 81949736603d22cac779d08d14bd6db65065d730
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92459088"
---
# <a name="tutorial-azure-active-directory-integration-with-lockpath-keylight"></a>Samouczek: integracja Azure Active Directory z programem LockPath

W tym samouczku dowiesz się, jak zintegrować LockPath z usługą Azure Active Directory (Azure AD).
Integracja LockPath z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do LockPath.
* Możesz pozwolić użytkownikom na automatyczne logowanie się do usługi LockPath (Logowanie jednokrotne) przy użyciu kont w usłudze Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z programem LockPath, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Subskrypcja z włączonym logowaniem jednokrotnym w LockPath

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* LockPath Lampa 
* LockPath — Lampa czasowa obsługuje Inicjowanie obsługi użytkowników **just in Time**

## <a name="adding-lockpath-keylight-from-the-gallery"></a>Dodawanie sygnalizatora LockPath z galerii

Aby skonfigurować integrację programu LockPath z usługą Azure AD, musisz dodać lampę LockPath z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać lampę LockPath z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **LockPath**, wybierz pozycję **LockPath sygnalizator** z panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![Lampka LockPath na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą LockPathego, opartego na użytkowniku testowym o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w LockPath.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu programu LockPath, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w usłudze LockPath](#configure-lockpath-keylight-single-sign-on)** , aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego LockPath](#create-lockpath-keylight-test-user)** , aby uzyskać odpowiednik Britta Simon w LockPath, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD przy użyciu programu LockPath, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **LockPath** , wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![LockPath — informacje o rejestracji jednokrotnej w domenie i adresach URL](common/sp-identifier-reply.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<company name>.keylightgrc.com/`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<company name>.keylightgrc.com`

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<company name>.keylightgrc.com/Login.aspx`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami adresu URL logowania, identyfikatora i adresu URL odpowiedzi. Skontaktuj się z [zespołem pomocy technicznej LockPath](https://www.lockpath.com/contact/) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (nieprzetworzony)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificateraw.png)

6. W sekcji **Skonfiguruj lampę LockPath** , skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-lockpath-keylight-single-sign-on"></a>Konfiguruj LockPath pojedyncze Sign-On

1. Aby włączyć logowanie jednokrotne w LockPath, wykonaj następujące czynności:

    a. Zaloguj się do swojego konta LockPath jako administrator.

    b. W menu u góry kliknij pozycję **osoba**, a następnie wybierz pozycję **Konfiguracja oświetlenia**.

    ![Zrzut ekranu, na którym jest zaznaczona ikona "osoba" i "Konfiguracja oświetlenia" wybraną z listy rozwijanej.](./media/keylight-tutorial/401.png)

    c. W widoku drzewa po lewej stronie kliknij pozycję **SAML**.

    ![Zrzut ekranu pokazujący "S A M L" wybrany w widoku drzewa.](./media/keylight-tutorial/402.png)

    d. W oknie dialogowym **Ustawienia SAML** kliknij przycisk **Edytuj**.

    ![Zrzut ekranu przedstawiający okno Ustawienia "S A M L" z wybranym przyciskiem "Edytuj".](./media/keylight-tutorial/404.png)

1. Na stronie **Edytowanie ustawień SAML** wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/keylight-tutorial/405.png)

    a. Ustaw **uwierzytelnianie SAML** jako **aktywne**.

    b. W polu tekstowym **adres URL logowania dostawcy tożsamości** wklej wartość **adresu URL logowania** , która została skopiowana z Azure Portal.

    c. W polu tekstowym **adres URL wylogowania dostawcy tożsamości** wklej wartość **adresu URL wylogowania** , która została skopiowana z Azure Portal.

    d. Kliknij pozycję **Wybierz plik** , aby wybrać pobrany certyfikat LockPath, a następnie kliknij przycisk **Otwórz** , aby przekazać certyfikat.

    e. Ustaw **lokalizację identyfikatora użytkownika SAML** na **element NameIdentifier instrukcji subject**.

    f. Podaj **dostawcę usługi** , korzystając z następującego wzorca: `https://<CompanyName>.keylightgrc.com` .

    przykład Ustaw **aktywną** **obsługę administracyjną użytkowników** .

    h. Ustaw **Typ konta autozastrzeganie** na **pełny użytkownik**.

    i. Ustaw **rolę zabezpieczeń autoudostępnianie** wybierz opcję **użytkownik standardowy z SAML**.

    j. Skonfiguruj konfigurację **zabezpieczeń automatycznej obsługi**, wybierz pozycję **Standardowa konfiguracja użytkownika**.

    k. W polu tekstowym **atrybut wiadomości e-mail** wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` .

    l. W polu tekstowym **nazwa atrybutu imię** wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` .

    m. W polu tekstowym nazwisko **atrybutu** wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` .

    n. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz `brittasimon@yourcompanydomain.extension` . Na przykład BrittaSimon@contoso.com

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji Britta Simon do korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi LockPath.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **LockPath**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **LockPath**.

    ![Link LockPath na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-lockpath-keylight-test-user"></a>Utwórz użytkownika testowego LockPath

W tej sekcji użytkownik o nazwie Britta Simon jest tworzony w LockPath. LockPath Lampa czasowa obsługuje Inicjowanie obsługi użytkowników just in Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w LockPath, zostanie utworzony nowy po uwierzytelnieniu. Jeśli musisz ręcznie utworzyć użytkownika, musisz skontaktować się z [zespołem pomocy technicznej LockPath](https://www.lockpath.com/contact/).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka LockPath sygnalizatora w panelu dostępu należy automatycznie zalogować się do LockPath, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)