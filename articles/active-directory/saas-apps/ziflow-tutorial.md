---
title: 'Samouczek: integracja Azure Active Directory z usługą Ziflow | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Ziflow.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: 9a9e2298b6707304df96a2e954015459534abfa9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88546087"
---
# <a name="tutorial-azure-active-directory-integration-with-ziflow"></a>Samouczek: integracja Azure Active Directory z usługą Ziflow

W tym samouczku dowiesz się, jak zintegrować usługę Ziflow z usługą Azure Active Directory (Azure AD).
Integracja Ziflow z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do usługi Ziflow.
* Możesz pozwolić użytkownikom na automatyczne logowanie do Ziflow (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą Ziflow, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Subskrypcja z włączonym logowaniem jednokrotnym w Ziflow

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Ziflow obsługuje logowanie jednokrotne w usłudze **SP**

## <a name="adding-ziflow-from-the-gallery"></a>Dodawanie Ziflow z galerii

Aby skonfigurować integrację programu Ziflow z usługą Azure AD, musisz dodać Ziflow z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Ziflow z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Ziflow**, wybierz pozycję **Ziflow** from panel wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

     ![Ziflow na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą Ziflow na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Ziflow.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi Ziflow, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie](#configure-ziflow-single-sign-on)** jednokrotne w usłudze Ziflow, aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego Ziflow](#create-ziflow-test-user)** , aby uzyskać odpowiednik Britta Simon w Ziflow, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą Ziflow, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Ziflow** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Ziflow domenę i adresy URL Logowanie jednokrotne](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://ziflow-production.auth0.com/login/callback?connection=<UniqueID>`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `urn:auth0:ziflow-production:<UniqueID>`

    > [!NOTE]
    > Podane wyżej wartości nie są rzeczywiste. Wartość unikatowego identyfikatora w polu Identyfikator i adres URL logowania należy zaktualizować za pomocą wartości rzeczywistej, która została omówiona w dalszej części tego samouczka.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie Ziflow** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-ziflow-single-sign-on"></a>Konfigurowanie pojedynczego Sign-On Ziflow

1. W innym oknie przeglądarki sieci Web Zaloguj się, aby Ziflow jako administrator zabezpieczeń.

2. Kliknij awatar w prawym górnym rogu, a następnie kliknij pozycję **Zarządzaj kontem**.

    ![Zarządzanie konfiguracją Ziflow](./media/ziflow-tutorial/tutorial_ziflow_manage.png)

3. W lewym górnym rogu kliknij pozycję **Logowanie jednokrotne**.

    ![Ziflow — znak konfiguracji](./media/ziflow-tutorial/tutorial_ziflow_signon.png)

4. Na stronie **Single Sign-On**(Logowanie jednokrotne) wykonaj następujące czynności:

    ![Ziflow Konfiguracja pojedynczego](./media/ziflow-tutorial/tutorial_ziflow_page.png)

    a. Wybierz **Typ** jako **SAML 2.0**.

    b. W polu tekstowym **adres URL logowania** wklej wartość **adresu URL logowania**, który został skopiowany z Azure Portal.

    c. Przekaż certyfikat zakodowany Base-64 pobrany z Azure Portal do **certyfikatu podpisywania x509**.

    d. W polu tekstowym **Wyloguj adres URL** wklej wartość **adresu URL wylogowania**, która została skopiowana z Azure Portal.

    e. W sekcji **Ustawienia konfiguracji dla dostawcy identyfikatora** Skopiuj wyróżnioną unikatową wartość identyfikatora i Dołącz ją z identyfikatorem i adresem URL logowania w **podstawowej konfiguracji SAML** na Azure Portal.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz brittasimon@yourcompanydomain.extension . Na przykład BrittaSimon@contoso.com

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji Britta Simon do korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Ziflow.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **Ziflow**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **Ziflow**.

    ![Link Ziflow na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-ziflow-test-user"></a>Utwórz użytkownika testowego Ziflow

Aby umożliwić użytkownikom usługi Azure AD logowanie się w usłudze Ziflow, muszą one być obsługiwane w usłudze Ziflow. W Ziflow, Inicjowanie obsługi jest zadaniem ręcznym.

Aby aprowizować konto użytkownika, wykonaj następujące czynności:

1. Zaloguj się do Ziflow jako administrator zabezpieczeń.

2. Przejdź do **osób** znajdujących się na górze.

    ![Ziflow osoby konfiguracji](./media/ziflow-tutorial/tutorial_ziflow_people.png)

3. Kliknij przycisk **Dodaj** , a następnie kliknij przycisk **Dodaj użytkownika**.

    ![Ziflow — Dodawanie użytkownika](./media/ziflow-tutorial/tutorial_ziflow_add.png)

4. W oknie podręcznym **Dodaj użytkownika** wykonaj następujące czynności:

    ![Ziflow — Dodawanie użytkownika](./media/ziflow-tutorial/tutorial_ziflow_adduser.png)

    a. W polu tekstowym **Email** (Adres e-mail) wprowadź adres e-mail użytkownika, na przykład brittasimon@contoso.com.

    b. W polu tekstowym **imię i nazwisko** Wprowadź imię użytkownika, np. Britta.

    c. W polu tekstowym **nazwisko** Wprowadź nazwisko użytkownika, np. Simon.

    d. Wybierz rolę Ziflow.

    e. Kliknij pozycję **Dodaj 1 użytkownika**.

    > [!NOTE]
    > Właściciel konta usługi Azure Active Directory otrzyma wiadomość e-mail z linkiem umożliwiającym potwierdzenie konta, zanim stanie się ono aktywne.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Ziflow w panelu dostępu należy automatycznie zalogować się do Ziflow, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

