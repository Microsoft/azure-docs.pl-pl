---
title: 'Samouczek: integracja Azure Active Directory z linkiem Springer | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i łącza Springer.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/13/2019
ms.author: jeedes
ms.openlocfilehash: a203bbd65c07d21c7d96b59c57b441748ed5b9a9
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92127562"
---
# <a name="tutorial-azure-active-directory-integration-with-springer-link"></a>Samouczek: integracja Azure Active Directory z linkiem Springer

W tym samouczku dowiesz się, jak zintegrować łącze Springer z usługą Azure Active Directory (Azure AD).
Integracja Springer z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do linku Springer.
* Możesz umożliwić użytkownikom automatyczne logowanie do Springer linku (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację z usługą Azure AD za pomocą linku Springer, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja z włączonym logowaniem jednokrotnym w usłudze Springer

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz Logowanie jednokrotne usługi Azure AD i testujesz ją.

* Link Springer obsługuje logowanie jednokrotne z użyciem **SP** i **dostawcy tożsamości**

## <a name="adding-springer-link-from-the-gallery"></a>Dodawanie linku Springer z galerii

Aby skonfigurować integrację linku Springer w usłudze Azure AD, musisz dodać link Springer z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać łącze Springer z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Springer link**, wybierz pozycję **Springer link** z panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

     ![Link Springer na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą linku Springer na podstawie użytkownika testowego o nazwie **Britta Simon**.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu linku Springer, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania](#configure-springer-link-single-sign-on)** jednokrotnego w usłudze Springer, aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD przy użyciu linku Springer, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie **Springer łączenie** aplikacji wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia podstawową konfigurację języka SAML, w której można wprowadzić identyfikator, odpowiedź U R L i wybrać pozycję Zapisz.](common/idp-relay.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL: `https://fsso.springer.com`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL: `https://fsso.springer.com/federation/Consumer/metaAlias/SpringerServiceProvider`

    c. Kliknij pozycję **Ustaw dodatkowe adresy URL**.

    d. W polu tekstowym **Stan przekaźnika** wpisz adres URL: `https://link.springer.com`

5. Jeśli chcesz skonfigurować aplikację w trybie zainicjowania programu **SP** , wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia ustawienie dodatkowego U R LS, gdzie można wprowadzić znak U R L.](common/both-signonurl.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://fsso.springer.com/saml/login?idp=<entityID>&targetUrl=https://link.springer.com`

    > [!NOTE]
    > Wartość adresu URL logowania nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. `<entityID>` czy identyfikator usługi Azure AD został skopiowany z sekcji **Konfigurowanie linku Springer** , opisanej w dalszej części samouczka. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

6. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** kliknij ikonę kopiowania, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link pobierania metadanych](common/copy_metadataurl.png)

### <a name="configure-springer-link-single-sign-on"></a>Konfigurowanie pojedynczego Sign-On linku Springer

Aby skonfigurować Logowanie jednokrotne na stronie **linku Springer** , musisz wysłać skopiowany **adres URL metadanych federacji aplikacji** do [zespołu pomocy technicznej linku Springer](mailto:onlineservice@springernature.com). Zespół pomocy technicznej linku Springer używa tego adresu URL do prawidłowego skonfigurowania połączenia SSO protokołu SAML po obu stronach.

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

Ta sekcja umożliwia włączenie usługi Britta Simon w celu korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do linku Springer.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **link Springer**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz **łącze Springer**.

    ![Link Springer linku na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka link Springer w panelu dostępu należy automatycznie zalogować się do linku Springer, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

