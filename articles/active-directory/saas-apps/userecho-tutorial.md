---
title: 'Samouczek: integracja Azure Active Directory z usługą UserEcho | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i UserEcho.
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
ms.openlocfilehash: be52d220848a27c1307e71fdb7cc4dccfaeb9618
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92509466"
---
# <a name="tutorial-azure-active-directory-integration-with-userecho"></a>Samouczek: integracja Azure Active Directory z usługą UserEcho

W tym samouczku dowiesz się, jak zintegrować usługę UserEcho z usługą Azure Active Directory (Azure AD).
Integracja UserEcho z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do usługi UserEcho.
* Możesz pozwolić użytkownikom na automatyczne logowanie do UserEcho (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą UserEcho, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Subskrypcja z włączonym logowaniem jednokrotnym w UserEcho

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* UserEcho obsługuje logowanie jednokrotne w usłudze **SP**

## <a name="adding-userecho-from-the-gallery"></a>Dodawanie UserEcho z galerii

Aby skonfigurować integrację programu UserEcho z usługą Azure AD, musisz dodać UserEcho z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać UserEcho z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **UserEcho**, wybierz pozycję **UserEcho** from panel wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

     ![UserEcho na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą UserEcho na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w UserEcho.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi UserEcho, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie](#configure-userecho-single-sign-on)** jednokrotne w usłudze UserEcho, aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego UserEcho](#create-userecho-test-user)** , aby uzyskać odpowiednik Britta Simon w UserEcho, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą UserEcho, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **UserEcho** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![UserEcho domenę i adresy URL Logowanie jednokrotne](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<companyname>.userecho.com/`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<companyname>.userecho.com/saml/metadata/`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z [zespołem obsługi klienta UserEcho](https://feedback.userecho.com/) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie UserEcho** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-userecho-single-sign-on"></a>Konfigurowanie pojedynczego Sign-On UserEcho

1. W innym oknie przeglądarki Zaloguj się do witryny firmy UserEcho jako administrator.

2. Na pasku narzędzi u góry kliknij swoją nazwę użytkownika, aby rozwinąć menu, a następnie kliknij przycisk **Setup (Konfiguracja**).
   
    ![Zrzut ekranu przedstawia konfigurację wybraną z witryny UserEcho.](./media/userecho-tutorial/tutorial_userecho_06.png) 

3. Kliknij przycisk **integracji**.
   
    ![Zrzut ekranu przedstawia integracje wybrane z menu Ustawienia.](./media/userecho-tutorial/tutorial_userecho_07.png) 

4. Kliknij pozycję **Witryna sieci Web**, a następnie kliknij pozycję **Logowanie jednokrotne (SAML2)**.
   
    ![Zrzut ekranu przedstawia SAML2 logowania jednokrotnego wybrane z menu integracji.](./media/userecho-tutorial/tutorial_userecho_08.png) 

5. Na stronie Logowanie jednokrotne **(SAML)** wykonaj następujące czynności:
   
    ![Zrzut ekranu przedstawia stronę SAML logowania jednokrotnego, na której można wprowadzić podane wartości.](./media/userecho-tutorial/tutorial_userecho_09.png)
    
    a. Jako **włączony protokół SAML** wybierz pozycję **tak**.
    
    b. Wklej **adres URL logowania**, który został skopiowany z Azure Portal do pola tekstowego **adresu URL rejestracji jednokrotnej protokołu SAML** .
    
    c. Wklej **adres URL wylogowania**, który został skopiowany z Azure Portal w polu tekstowym **adres URL wylogowywania zdalnego** .
    
    d. Otwórz pobrany certyfikat w Notatniku, skopiuj zawartość, a następnie wklej ją do pola tekstowego **certyfikatu X. 509** .
    
    e. Kliknij pozycję **Zapisz**.

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

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji Britta Simon do korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi UserEcho.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **UserEcho**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **UserEcho**.

    ![Link UserEcho na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-userecho-test-user"></a>Utwórz użytkownika testowego UserEcho

Celem tej sekcji jest utworzenie użytkownika o nazwie Britta Simon w UserEcho.

**Aby utworzyć użytkownika o nazwie Britta Simon in UserEcho, wykonaj następujące czynności:**

1. Zaloguj się do firmowej witryny UserEcho jako administrator.

2. Na pasku narzędzi u góry kliknij swoją nazwę użytkownika, aby rozwinąć menu, a następnie kliknij przycisk **Setup (Konfiguracja**).
   
    ![Zrzut ekranu przedstawia konfigurację wybraną z witryny UserEcho.](./media/userecho-tutorial/tutorial_userecho_06.png)

3. Kliknij pozycję **Użytkownicy**, aby rozwinąć sekcję **Użytkownicy** .
   
    ![Zrzut ekranu przedstawia użytkowników wybranych w menu Ustawienia.](./media/userecho-tutorial/tutorial_userecho_10.png)

4. Kliknij pozycję **Użytkownicy**.
   
    ![Zrzut ekranu przedstawia wybrane użytkowników.](./media/userecho-tutorial/tutorial_userecho_11.png)

5. Kliknij pozycję **Zaproś nowego użytkownika**.
   
    ![Zrzut ekranu przedstawia zaproszenie nowej kontrolki użytkownika.](./media/userecho-tutorial/tutorial_userecho_12.png)

6. W oknie dialogowym **zapraszanie nowego użytkownika** wykonaj następujące czynności:
   
    ![Zrzut ekranu przedstawia okno dialogowe Zaproś nowego użytkownika, w którym można wprowadzić informacje o użytkowniku.](./media/userecho-tutorial/tutorial_userecho_13.png)

    a. W polu tekstowym **Nazwa** wpisz nazwę użytkownika, na przykład Britta Simon.
    
    b.  W polu tekstowym **Email** (Adres e-mail) wpisz adres e-mail użytkownika, np. Brittasimon@contoso.com.
    
    c. Kliknij pozycję **Invite** (Zaproś).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka UserEcho w panelu dostępu należy automatycznie zalogować się do UserEcho, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)