---
title: 'Samouczek: integracja Azure Active Directory z usługą Voyance | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Voyance.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/07/2019
ms.author: jeedes
ms.openlocfilehash: 51034fb5da5c8fd69ab3bb2b770a9bc88b90488b
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92636038"
---
# <a name="tutorial-azure-active-directory-integration-with-voyance"></a>Samouczek: integracja Azure Active Directory z usługą Voyance

W tym samouczku dowiesz się, jak zintegrować usługę Voyance z usługą Azure Active Directory (Azure AD).
Integracja Voyance z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do usługi Voyance.
* Możesz pozwolić użytkownikom na automatyczne logowanie do Voyance (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą Voyance, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Subskrypcja z włączonym logowaniem jednokrotnym w Voyance

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Voyance obsługuje usługę **SP** i **dostawcy tożsamości** zainicjowano Logowanie jednokrotne

* Voyance obsługuje Inicjowanie obsługi użytkowników **just in Time**

## <a name="adding-voyance-from-the-gallery"></a>Dodawanie Voyance z galerii

Aby skonfigurować integrację programu Voyance z usługą Azure AD, musisz dodać Voyance z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Voyance z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory** .

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje** .

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Voyance** , wybierz pozycję **Voyance** from panel wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

     ![Voyance na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą Voyance na podstawie użytkownika testowego o nazwie **Britta Simon** .
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Voyance.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi Voyance, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie](#configure-voyance-single-sign-on)** jednokrotne w usłudze Voyance, aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego Voyance](#create-voyance-test-user)** , aby uzyskać odpowiednik Britta Simon w Voyance, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą Voyance, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Voyance** wybierz pozycję **Logowanie jednokrotne** .

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed** , aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj** , aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML** .

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia podstawową konfigurację języka SAML, w której można wprowadzić identyfikator, odpowiedź U R L i wybrać pozycję Zapisz.](common/idp-intiated.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://<companyname>.nyansa.com`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<companyname>.nyansa.com/saml/create/`

5. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    ![Zrzut ekranu przedstawia ustawienie dodatkowego U R LS, gdzie można wprowadzić znak U R L.](common/metadata-upload-additional-signon.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<companyname>.nyansa.com/`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z [zespołem obsługi klienta Voyance](mailto:support@nyansa.com) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz** , aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

7. W sekcji **Konfigurowanie Voyance** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-voyance-single-sign-on"></a>Konfigurowanie pojedynczego Sign-On Voyance

1. W innym oknie przeglądarki sieci Web Zaloguj się do swojej dzierżawy Voyance jako administrator.

2. Przejdź do prawego górnego rogu paska nawigacyjnego, a następnie kliknij pozycję **profil** .
    
    ![Konfigurowanie pojedynczej Sign-On na Uniwersytecie](./media/voyance-tutorial/tutorial_voyance_001.png) 

3. Kliknij pozycję **Ustawienia administratora** .

    ![Skonfiguruj pojedyncze Sign-On w ustawieniach administratora po stronie aplikacji](./media/voyance-tutorial/tutorial_voyance_002.png)

4. Kliknij kartę **dostęp użytkownika** .

    ![Skonfiguruj pojedyncze Sign-On w dostępie użytkownika po stronie aplikacji](./media/voyance-tutorial/tutorial_voyance_003.png)

5. Kliknij przycisk **Logowanie jednokrotne** , aby skonfigurować usługę Azure AD jako dostawcy tożsamości przy użyciu protokołu SAML 2,0.

    ![Przycisk Konfiguruj pojedyncze Sign-On po stronie logowania jednokrotnego jest wyłączony](./media/voyance-tutorial/tutorial_voyance_004.png)

6. Przejdź do sekcji **SAML V2** i wykonaj następujące czynności:

    ![Konfigurowanie jednego Sign-On na stronie aplikacji SAML V2](./media/voyance-tutorial/tutorial-voyance-005.png)
    
    a. Wybierz pozycję **Włączone** .
    
    b. Wklej **adres URL logowania** , który został skopiowany z Azure Portal do pola tekstowego **adres URL logowania dostawcy tożsamości** .

    c. Otwórz pobrany certyfikat zakodowany w formacie base64 w Notatniku, skopiuj zawartość tego pliku do schowka, a następnie wklej go do pola tekstowego **certyfikatu dostawcy tożsamości** .
    
    d. Kliknij pozycję **Zapisz** .

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory** , wybierz opcję **Użytkownicy** , a następnie wybierz pozycję **Wszyscy użytkownicy** .

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon** .
  
    b. W polu **Nazwa użytkownika** wpisz brittasimon@yourcompanydomain.extension . Na przykład BrittaSimon@contoso.com

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz** .

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji Britta Simon do korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Voyance.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw** , wybierz pozycję **wszystkie aplikacje** , a następnie wybierz pozycję **Voyance** .

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **Voyance** .

    ![Link Voyance na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy** .

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika** , a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania** .

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

### <a name="create-voyance-test-user"></a>Utwórz użytkownika testowego Voyance

W tej sekcji użytkownik o nazwie Britta Simon jest tworzony w Voyance. Voyance obsługuje Inicjowanie obsługi użytkowników just in Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w usłudze Voyance, zostanie utworzony nowy po uwierzytelnieniu.

>[!NOTE]
>Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej Voyance](maiLto:support@nyansa.com).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Voyance w panelu dostępu należy automatycznie zalogować się do Voyance, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)