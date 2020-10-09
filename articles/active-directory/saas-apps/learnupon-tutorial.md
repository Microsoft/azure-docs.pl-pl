---
title: 'Samouczek: integracja Azure Active Directory z usługą LearnUpon | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i LearnUpon.
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
ms.openlocfilehash: 47ecf00b608121271e98ca5abbbd5e69ca811fd2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91858238"
---
# <a name="tutorial-azure-active-directory-integration-with-learnupon"></a>Samouczek: integracja Azure Active Directory z usługą LearnUpon

W tym samouczku dowiesz się, jak zintegrować usługę LearnUpon z usługą Azure Active Directory (Azure AD).
Integracja LearnUpon z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do usługi LearnUpon.
* Możesz pozwolić użytkownikom na automatyczne logowanie do LearnUpon (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą LearnUpon, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Subskrypcja z włączonym logowaniem jednokrotnym w LearnUpon

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.


* Usługa LearnUpon obsługuje **dostawcy tożsamości** zainicjowane przez logowanie jednokrotne

* LearnUpon obsługuje Inicjowanie obsługi użytkowników **just in Time**


## <a name="adding-learnupon-from-the-gallery"></a>Dodawanie LearnUpon z galerii

Aby skonfigurować integrację programu LearnUpon z usługą Azure AD, musisz dodać LearnUpon z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać LearnUpon z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **LearnUpon**, wybierz pozycję **LearnUpon** from panel wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![LearnUpon na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą LearnUpon na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w LearnUpon.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi LearnUpon, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie](#configure-learnupon-single-sign-on)** jednokrotne w usłudze LearnUpon, aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego LearnUpon](#create-learnupon-test-user)** , aby uzyskać odpowiednik Britta Simon w LearnUpon, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą LearnUpon, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **LearnUpon** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![LearnUpon domenę i adresy URL Logowanie jednokrotne](common/idp-reply.png)

    W polu tekstowym **adres URL odpowiedzi** wpisz adres URL, używając następującego wzorca:  `https://<companyname>.learnupon.com/saml/consumer`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zaktualizuj ją, stosując rzeczywisty adres URL odpowiedzi. Skontaktuj się z [zespołem obsługi klienta LearnUpon](https://www.learnupon.com/features/support/) , aby uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** Znajdź **odcisk palca** — zostanie on dodany do ustawień SAML LearnUpon.

    ![Link do pobierania certyfikatu](common/certificateraw.png)

6. W sekcji **Konfigurowanie LearnUpon** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-learnupon-single-sign-on"></a>Konfigurowanie pojedynczego Sign-On LearnUpon

1. Otwórz inne wystąpienie przeglądarki i zaloguj się do LearnUpon przy użyciu konta administratora.

1. Kliknij kartę **Ustawienia** .

    ![Zrzut ekranu przedstawia kartę Ustawienia.](./media/learnupon-tutorial/tutorial_learnupon_06.png)

1. Kliknij pozycję Logowanie jednokrotne **— SAML**, a następnie kliknij pozycję **Ustawienia ogólne** , aby skonfigurować ustawienia protokołu SAML.
   
    ![Zrzut ekranu przedstawia Logowanie jednokrotne — obiekt SAML wybrany z ustawieniami ogólnymi.](./media/learnupon-tutorial/tutorial_learnupon_07.png) 

1. W sekcji **Ustawienia ogólne** wykonaj następujące czynności:
   
    ![Zrzut ekranu przedstawia sekcję ustawień ogólnych, w której można wprowadzić podane wartości.](./media/learnupon-tutorial/tutorial_learnupon_08.png)  
  
    a. Wybierz pozycję **Włączone**.

    b. Wybierz **wersję** jako **2,0**.

    c. Wybierz pozycję **Pomiń warunki** jako **nie**.

    d. W polu tekstowym **Nazwa wpisu elementu końcowego tokenu SAML** wpisz nazwę parametru POST żądania do adresu URL odbiorcy SAML wskazanego powyżej, który zawiera potwierdzenie SAML do zweryfikowania i uwierzytelnienia — na przykład **SAMLResponse**.

    e. W polu tekstowym **Format identyfikatora nazwy** wpisz wartość wskazującą, gdzie w potwierdzeniu SAML znajduje się identyfikator użytkownika (adres e-mail), na przykład `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress` .
  
    f. W polu tekstowym **Zidentyfikuj lokalizację dostawcy** wpisz wartość wskazującą miejsce, do którego użytkownicy są wysyłani, jeśli klikniesz zakazaną ikonę na ekranie logowania Azure Portal.
  
    przykład W polu tekstowym **Wyloguj adres** URL wklej wartość **adresu URL wylogowania** , która została skopiowana z Azure Portal.

    h. Kliknij kolejno pozycje **Zarządzaj drukowaniem palca**, a następnie przekazanie przez niego odcisku palca pobranego certyfikatu.

1. Kliknij pozycję **Ustawienia użytkownika**, a następnie wykonaj następujące czynności:

     ![Zrzut ekranu przedstawia sekcję Ustawienia użytkownika, w której można wprowadzić podane wartości.](./media/learnupon-tutorial/tutorial_learnupon_11.png)  

    a. W polu tekstowym **Format identyfikatora pierwszej nazwy** wpisz wartość informującą nas, gdzie w potwierdzeniu SAML znajdują się użytkownicy FirstName — na przykład: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` .
  
    b. W polu tekstowym **Identyfikator ostatniego identyfikatora nazwy** wpisz wartość informującą nas, gdzie w potwierdzeniu SAML znajduje się nazwisko użytkownika, na przykład: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` .

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

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji Britta Simon do korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi LearnUpon.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **LearnUpon**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **LearnUpon**.

    ![Link LearnUpon na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-learnupon-test-user"></a>Utwórz użytkownika testowego LearnUpon

W tej sekcji użytkownik o nazwie Britta Simon jest tworzony w LearnUpon. LearnUpon obsługuje Inicjowanie obsługi użytkowników just in Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w usłudze LearnUpon, zostanie utworzony nowy po uwierzytelnieniu. Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej LearnUpon](https://www.learnupon.com/features/support/).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka LearnUpon w panelu dostępu należy automatycznie zalogować się do LearnUpon, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)