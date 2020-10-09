---
title: 'Samouczek: integracja Azure Active Directory z usługą StatusPage | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i StatusPage.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: 2ee2eb7586489527272d92dd1eaef6976bc41f94
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88524834"
---
# <a name="tutorial-azure-active-directory-integration-with-statuspage"></a>Samouczek: integracja Azure Active Directory z usługą StatusPage

W tym samouczku dowiesz się, jak zintegrować usługę StatusPage z usługą Azure Active Directory (Azure AD).
Integracja StatusPage z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do usługi StatusPage.
* Możesz pozwolić użytkownikom na automatyczne logowanie do StatusPage (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą StatusPage, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Subskrypcja z włączonym logowaniem jednokrotnym w StatusPage

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa StatusPage obsługuje **dostawcy tożsamości** zainicjowane przez logowanie jednokrotne

## <a name="adding-statuspage-from-the-gallery"></a>Dodawanie StatusPage z galerii

Aby skonfigurować integrację programu StatusPage z usługą Azure AD, musisz dodać StatusPage z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać StatusPage z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **StatusPage**, wybierz pozycję **StatusPage** from panel wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![StatusPage na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą StatusPage na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w StatusPage.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi StatusPage, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie](#configure-statuspage-single-sign-on)** jednokrotne w usłudze StatusPage, aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego StatusPage](#create-statuspage-test-user)** , aby uzyskać odpowiednik Britta Simon w StatusPage, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą StatusPage, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **StatusPage** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** wykonaj następujące kroki:

    ![StatusPage domenę i adresy URL Logowanie jednokrotne](common/idp-intiated.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: 

    - `https://<subdomain>.statuspagestaging.com/`
    - `https://<subdomain>.statuspage.io/`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: 

    - `https://<subdomain>.statuspagestaging.com/sso/saml/consume`
    - `https://<subdomain>.statuspage.io/sso/saml/consume`

    > [!NOTE]
    > Skontaktuj się z zespołem pomocy technicznej StatusPage pod adresem, [SupportTeam@statuspage.io](mailto:SupportTeam@statuspage.io) Aby zażądać metadanych niezbędnych do skonfigurowania logowania jednokrotnego. 
    >
    > a. Z metadanych skopiuj wartość wystawcy, a następnie wklej ją do pola tekstowego **identyfikatora** .
    >
    > b. Skopiuj adres URL odpowiedzi z metadanych, a następnie wklej go do pola tekstowego **adres URL odpowiedzi** .

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie StatusPage** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-statuspage-single-sign-on"></a>Konfigurowanie pojedynczego Sign-On StatusPage

1. W innym oknie przeglądarki Zaloguj się do witryny firmy StatusPage jako administrator.

1. Na głównym pasku narzędzi kliknij pozycję **Zarządzaj kontem**.

    ![Konfigurowanie logowania jednokrotnego](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. Kliknij kartę **Logowanie** jednokrotne.

    ![Konfigurowanie logowania jednokrotnego](./media/statuspage-tutorial/tutorial_statuspage_07.png)

1. Na stronie Konfiguracja logowania jednokrotnego wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/statuspage-tutorial/tutorial_statuspage_08.png)

    ![Konfigurowanie logowania jednokrotnego](./media/statuspage-tutorial/tutorial_statuspage_09.png)

    a. W polu tekstowym **adres URL elementu docelowego logowania jednokrotnego** wklej wartość **adresu URL logowania**, która została skopiowana z Azure Portal.

    b. Otwórz pobrany certyfikat w Notatniku, skopiuj zawartość, a następnie wklej ją do pola tekstowego **certyfikatu** .

    c. Kliknij pozycję **Zapisz konfigurację**.

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

W tej sekcji Britta Simon do korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi StatusPage.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **StatusPage**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **StatusPage**.

    ![Link StatusPage na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-statuspage-test-user"></a>Utwórz użytkownika testowego StatusPage

Celem tej sekcji jest utworzenie użytkownika o nazwie Britta Simon w StatusPage.

StatusPage obsługuje Inicjowanie obsługi just-in-Time. Włączono już ją w obszarze [Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on).

**Aby utworzyć użytkownika o nazwie Britta Simon in StatusPage, wykonaj następujące czynności:**

1. Zaloguj się do firmowej witryny StatusPage jako administrator.

1. W menu u góry kliknij pozycję **Zarządzaj kontem**.

    ![Konfigurowanie logowania jednokrotnego](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. Kliknij kartę **członkowie zespołu** .
  
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/statuspage-tutorial/tutorial_statuspage_10.png) 

1. Kliknij pozycję **Dodaj członka zespołu**.
  
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/statuspage-tutorial/tutorial_statuspage_11.png) 

1. Wpisz **adres e-mail**, **imię**i **nazwisko** poprawnego użytkownika, który chcesz udostępnić do powiązanych pól tekstowych. 

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/statuspage-tutorial/tutorial_statuspage_12.png) 

1. Jako **rolę**wybierz pozycję **administrator klienta**.

1. Kliknij pozycję **Utwórz konto**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka StatusPage w panelu dostępu należy automatycznie zalogować się do StatusPage, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
