---
title: 'Samouczek: integracja Azure Active Directory z jednym | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Zoho.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: fa63cc2c76d8bd47ca80050a369bda7211f5db24
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92896757"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho-one"></a>Samouczek: Azure Active Directory integrację z usługą Zoho one

W tym samouczku dowiesz się, jak zintegrować usługę Zoho z usługą Azure Active Directory (Azure AD).
Integracja Zoho z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do Zoho jednego.
* Możesz pozwolić użytkownikom na automatyczne logowanie się, aby Zoho jeden (Logowanie jednokrotne) przy użyciu swoich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą Zoho, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Zoho jedną subskrypcję z włączonym logowaniem jednokrotnym

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Zoho jeden obsługuje usługę **SP** i **dostawcy tożsamości** zainicjowano Logowanie jednokrotne

## <a name="adding-zoho-one-from-the-gallery"></a>Dodawanie Zoho jednego z galerii

Aby skonfigurować integrację programu Zoho z usługą Azure AD, musisz dodać Zoho jeden z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Zoho jeden z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory** .

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje** .

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Zoho** , a następnie wybierz pozycję **Zoho** z panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

     ![Zoho jeden na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą Zoho jednej na podstawie użytkownika testowego o nazwie **Britta Simon** .
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Zoho.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Zoho, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj jedno Logowanie jednokrotne](#configure-zoho-one-single-sign-on)** w usłudze Zoho, aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz Zoho jednego użytkownika testowego](#create-zoho-one-test-user)** — Aby uzyskać odpowiednik Britta Simon w Zoho, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą Zoho, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie **Zoho jednej** aplikacji wybierz pozycję **Logowanie jednokrotne** .

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed** , aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj** , aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML** .

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia podstawową konfigurację języka SAML, w której można wprowadzić identyfikator, odpowiedź U R L i wybrać pozycję Zapisz.](common/idp-relay.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL: `one.zoho.com`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://accounts.zoho.com/samlresponse/<saml-identifier>`

    > [!NOTE]
    > Poprzednia wartość **adresu URL odpowiedzi** nie jest prawdziwa. Otrzymasz `<saml-identifier>` wartość z #step4 **konfigurowania Zoho jednej** jednokrotnej rejestracji, która została omówiona w dalszej części tego samouczka.

    c. Kliknij pozycję **Ustaw dodatkowe adresy URL** .

    d. W polu tekstowym **Stan przekaźnika** wpisz adres URL: `https://one.zoho.com`

5. Jeśli chcesz skonfigurować aplikację w trybie zainicjowania programu **SP** , wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia ustawienie dodatkowego U R LS, gdzie można wprowadzić znak U R L.](common/both-signonurl.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://accounts.zoho.com/samlauthrequest/<domain_name>?serviceurl=https://one.zoho.com` 

    > [!NOTE] 
    > Poprzednia wartość **adresu URL logowania** nie jest prawdziwa. Wartość zostanie zaktualizowana o rzeczywisty adres URL Sign-On w sekcji **Configure Zoho one Single Sign-on** , która została omówiona w dalszej części tego samouczka. 

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz** , aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

7. Na stronie **Konfigurowanie Zoho jednej** sekcji skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-zoho-one-single-sign-on"></a>Konfigurowanie jednego Sign-On Zoho

1. W innym oknie przeglądarki sieci Web Zaloguj się do swojej Zoho jednej witryny firmowej jako administrator.

2. Na karcie **organizacja** kliknij pozycję **Instalator** w obszarze **uwierzytelnianie SAML** .

    ![Zoho jednej organizacji](./media/zohoone-tutorial/tutorial_zohoone_setup.png)

3. Na stronie podręcznej wykonaj następujące czynności:

    ![Zoho jeden SIG](./media/zohoone-tutorial/tutorial_zohoone_save.png)

    a. W polu tekstowym **adres URL logowania** wklej wartość **adresu URL logowania** , który został skopiowany z Azure Portal.

    b. W polu tekstowym **adres URL wylogowania** wklej wartość **adresu URL wylogowania** , która została skopiowana z Azure Portal.

    c. Kliknij przycisk **Przeglądaj** , aby przekazać **certyfikat (base64)** pobrany z Azure Portal.

    d. Kliknij pozycję **Zapisz** .

4. Po zapisaniu konfiguracji uwierzytelniania SAML skopiuj wartość **identyfikatora SAML** i Dołącz ją z **adresem URL odpowiedzi** zamiast `<saml-identifier>` , podobnie jak `https://accounts.zoho.com/samlresponse/one.zoho.com` i wklej WYGENEROWANĄ wartość w polu tekstowym **adres URL odpowiedzi** w sekcji **Podstawowa konfiguracja SAML** .

    ![Zoho jeden element SAML](./media/zohoone-tutorial/tutorial_zohoone_samlidenti.png)

5. Przejdź do karty **domeny** , a następnie kliknij pozycję **Dodaj domenę** .

    ![Zoho jedną domenę](./media/zohoone-tutorial/tutorial_zohoone_domain.png)

6. Na stronie **Dodawanie domeny** wykonaj następujące czynności:

    ![Zoho jedną domenę](./media/zohoone-tutorial/tutorial_zohoone_adddomain.png)

    a. W polu tekstowym **nazwa domeny** wpisz domena, na przykład contoso.com.

    b. Kliknij pozycję **Dodaj** .

    >[!Note]
    >Po dodaniu domeny wykonaj [następujące](https://www.zoho.com/one/help/admin-guide/domain-verification.html) kroki, aby zweryfikować domenę. Po zweryfikowaniu domeny Użyj nazwy domeny w **adresie URL logowania** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory** , wybierz opcję **Użytkownicy** , a następnie wybierz pozycję **Wszyscy użytkownicy** .

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon** .
  
    b. W polu **Nazwa użytkownika** wpisz `brittasimon@yourcompanydomain.extension` . Na przykład BrittaSimon@contoso.com

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz** .

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji Britta Simon do korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do Zoho.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw** , wybierz pozycję **wszystkie aplikacje** , a następnie wybierz pozycję **Zoho** .

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **Zoho jeden** .

    ![Łącze Zoho 1 na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy** .

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika** , a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania** .

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

### <a name="create-zoho-one-test-user"></a>Utwórz Zoho jednego użytkownika testowego

Aby umożliwić użytkownikom usługi Azure AD logowanie się w celu Zoho jednego, muszą one być obsługiwane w Zoho. W Zoho jednym, aprowizacji jest zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się, aby Zoho jeden jako administrator zabezpieczeń.

2. Na karcie **Użytkownicy** kliknij pozycję **logo użytkownika** .

    ![Zoho jednego użytkownika](./media/zohoone-tutorial/tutorial_zohoone_users.png)

3. Na stronie **Dodawanie użytkownika** wykonaj następujące czynności:

    ![Zoho jednego użytkownika](./media/zohoone-tutorial/tutorial_zohoone_adduser.png)
    
    a. W polu tekstowym **Nazwa** wprowadź nazwę użytkownika, np. **Britta Simon** .
    
    b. W polu tekstowym **Email Address** (Adres e-mail) wprowadź adres e-mail użytkownika, na przykład brittasimon@contoso.com.

    >[!Note]
    >Wybierz zweryfikowaną domenę z listy domen.

    c. Kliknij pozycję **Dodaj** .

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu jednego kafelka Zoho w panelu dostępu należy automatycznie zalogować się do Zoho, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)