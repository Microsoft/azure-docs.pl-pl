---
title: 'Samouczek: integracja Azure Active Directory z oprogramowaniem Igloo | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory a oprogramowaniem Igloo.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/06/2019
ms.author: jeedes
ms.openlocfilehash: 033561ac3c4a510927691dc8db4f61196f54ec2a
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92460397"
---
# <a name="tutorial-azure-active-directory-integration-with-igloo-software"></a>Samouczek: integracja Azure Active Directory z oprogramowaniem Igloo

W tym samouczku dowiesz się, jak zintegrować oprogramowanie Igloo z usługą Azure Active Directory (Azure AD).
Integracja oprogramowania Igloo z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do oprogramowania Igloo.
* Możesz pozwolić użytkownikom na automatyczne logowanie do Igloo oprogramowania (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z oprogramowaniem Igloo, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja z włączonym logowaniem jednokrotnym w Igloo

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Oprogramowanie Igloo obsługuje logowanie jednokrotne zainicjowane przez usługę **SP**
* Oprogramowanie Igloo obsługuje Inicjowanie obsługi użytkowników **just in Time**

## <a name="adding-igloo-software-from-the-gallery"></a>Dodawanie oprogramowania Igloo z galerii

Aby skonfigurować integrację oprogramowania Igloo w usłudze Azure AD, musisz dodać oprogramowanie Igloo z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać oprogramowanie Igloo z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Igloo oprogramowanie**, wybierz pozycję **Igloo oprogramowanie** z panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

     ![Igloo oprogramowanie na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą oprogramowania Igloo na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w oprogramowaniu Igloo.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu oprogramowania Igloo, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie](#configure-igloo-software-single-sign-on)** jednokrotne w programie Igloo, aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego oprogramowania Igloo](#create-igloo-software-test-user)** , aby dysponować odpowiednikiem Britta Simon w oprogramowaniu Igloo, które jest połączone z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD przy użyciu oprogramowania Igloo, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Igloo oprogramowania** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Igloo oprogramowania i adresów URL Logowanie jednokrotne](common/sp-identifier-reply.png)

    a. W polu tekstowym **adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<company name>.igloocommmunities.com`

    b. W polu **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://<company name>.igloocommmunities.com/saml.digest`

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<company name>.igloocommmunities.com/saml.digest`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami adresu URL logowania, identyfikatora i adresu URL odpowiedzi. Skontaktuj się z [zespołem obsługi klienta oprogramowania Igloo](https://www.igloosoftware.com/services/support) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie oprogramowania Igloo** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-igloo-software-single-sign-on"></a>Skonfiguruj pojedyncze Sign-On oprogramowania Igloo

1. W innym oknie przeglądarki sieci Web Zaloguj się do swojej witryny firmowej Igloo jako administrator.

2. Przejdź do **Panelu sterowania**.

     ![Panel sterowania](./media/igloo-software-tutorial/ic799949.png "Panel sterowania")

3. Na karcie **członkostwo** kliknij pozycję **Ustawienia logowania**.

    ![Ustawienia logowania](./media/igloo-software-tutorial/ic783968.png "Ustawienia logowania")

4. W sekcji Konfiguracja protokołu SAML kliknij pozycję **Konfiguruj uwierzytelnianie SAML**.

    ![Konfiguracja SAML](./media/igloo-software-tutorial/ic783969.png "Konfiguracja SAML")

5. W sekcji **Konfiguracja ogólna** wykonaj następujące czynności:

    ![Konfiguracja ogólna](./media/igloo-software-tutorial/ic783970.png "Konfiguracja ogólna")

    a. W polu tekstowym **Nazwa połączenia** wpisz niestandardową nazwę konfiguracji.

    b. W polu tekstowym **adres URL logowania dostawcy tożsamości** wklej wartość **adresu URL logowania** skopiowanego z Azure Portal.

    c. W polu tekstowym **adres URL wylogowywania dostawcy tożsamości** wklej wartość **adresu URL wylogowywania** skopiowanego z Azure Portal.

    d. Wybierz pozycję **odpowiedź na wylogowanie i zażądaj typu http** jako **post**.

    e. Otwórz certyfikat zakodowany w formacie **Base-64** w programie Notepad pobranym z Azure Portal Skopiuj zawartość tego pliku do schowka, a następnie wklej go do pola tekstowego **certyfikatu publicznego** .

6. W **konfiguracji odpowiedzi i uwierzytelniania**wykonaj następujące czynności:

    ![Konfiguracja odpowiedzi i uwierzytelniania](./media/igloo-software-tutorial/IC783971.png "Konfiguracja odpowiedzi i uwierzytelniania")
  
    a. Jako **dostawcę tożsamości**wybierz pozycję **Microsoft AD FS**.

    b. Jako **Typ identyfikatora**wybierz opcję **adres e-mail**. 

    c. W polu tekstowym **atrybut wiadomości e-mail** wpisz **EmailAddress**.

    d. W polu tekstowym **nazwa atrybutu imięname** . **givenname**

    e. W polu tekstowym nazwisko **atrybutu** wpisz **nazwisko**.

7. Wykonaj następujące kroki, aby ukończyć konfigurację:

    ![Tworzenie użytkownika podczas logowania](./media/igloo-software-tutorial/IC783972.png "Tworzenie użytkownika podczas logowania") 

    a. Podczas **tworzenia użytkownika podczas logowania**wybierz pozycję **Utwórz nowego użytkownika w Twojej witrynie podczas logowania**.

    b. W **obszarze Ustawienia logowania**wybierz pozycję **Użyj przycisku SAML na ekranie "Logowanie"**.

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
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon@yourcompanydomain.extension**  
    Na przykład BrittaSimon@contoso.com

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

Ta sekcja umożliwia włączenie usługi Britta Simon w celu korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do oprogramowania Igloo.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **Igloo oprogramowanie**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **Igloo oprogramowanie**.

    ![Link Igloo Software na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-igloo-software-test-user"></a>Utwórz użytkownika testowego Igloo oprogramowania

Nie ma elementu akcji, aby skonfigurować Inicjowanie obsługi administracyjnej użytkowników w oprogramowaniu Igloo.  

Gdy przypisany użytkownik próbuje zalogować się do Igloo oprogramowania przy użyciu panelu dostępu, Igloo oprogramowanie sprawdza, czy użytkownik istnieje.  Jeśli nie ma jeszcze dostępnego konta użytkownika, jest ono automatycznie tworzone przez oprogramowanie Igloo.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka oprogramowanie Igloo w panelu dostępu należy automatycznie zalogować się do oprogramowania Igloo, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)