---
title: 'Samouczek: integracja Azure Active Directory z FM: systemy | Microsoft Docs'
description: 'Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i FM: System.'
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/05/2019
ms.author: jeedes
ms.openlocfilehash: a43c1092cd916877b24fc145ed05c92f50127cca
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92452732"
---
# <a name="tutorial-azure-active-directory-integration-with-fmsystems"></a>Samouczek: integracja Azure Active Directory z FM: systemy

W tym samouczku dowiesz się, jak zintegrować FM: systemy z Azure Active Directory (Azure AD).
Integrowanie FM: systemy z usługą Azure AD zapewniają następujące korzyści:

* W usłudze Azure AD można kontrolować, kto ma dostęp do systemów FM:.
* Możesz umożliwić użytkownikom automatyczne logowanie do systemu FM: systemy (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację z usługą Azure AD przy użyciu FM: Systems, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* FM: subskrypcja z włączonym logowaniem jednokrotnym dla systemów

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* FM: systemy obsługują zainicjowane Logowanie jednokrotne w **dostawcy tożsamości**

## <a name="adding-fmsystems-from-the-gallery"></a>Dodawanie FM: systemy z galerii

Aby skonfigurować integrację FM: systemów z usługą Azure AD, należy dodać FM: systemy z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać FM: systemy z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **FM: Systems**, wybierz pozycję **FM: Systems** z panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

     ![FM: systemy na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD przy użyciu FM: systemów opartych na użytkowniku testowym o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, relacja linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w FM: należy ustanowić systemy.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD przy użyciu systemu FM:, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania](#configure-fmsystems-single-sign-on)** jednokrotnego (FM): systemy — aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz Radio: systemy testowe użytkownika](#create-fmsystems-test-user)** — Aby uzyskać odpowiednik Britta Simon w FM: systemach, które są połączone z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne w usłudze Azure AD przy użyciu systemu FM:, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja z aplikacjami **UKF: system** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![FM: domeny systemów i adresów URL Logowanie jednokrotne](common/both-replyurl.png)

    W polu tekstowym **adres URL odpowiedzi** wpisz adres URL, używając następującego wzorca:  `https://<companyname>.fmshosted.com/fminteract/ConsumerService2.aspx`
    
    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zaktualizuj ją, stosując rzeczywisty adres URL odpowiedzi. Skontaktuj się z nami [: zespół obsługi klienta systemu](https://fmsystems.com/support-services/) , aby uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W sekcji **Konfigurowanie systemu FM: Systems** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-fmsystems-single-sign-on"></a>Konfigurowanie systemu FM: systemy pojedyncze Sign-On

Aby skonfigurować Logowanie jednokrotne w systemie **FM: Systems** , musisz wysłać pobrany **kod XML metadanych Federacji** i odpowiednie skopiowane adresy URL z Azure Portal do [FM: system obsługi zespołu](https://fmsystems.com/support-services/). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

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

Ta sekcja umożliwia włączenie usługi Britta Simon w celu korzystania z logowania jednokrotnego platformy Azure przez przyznanie dostępu do systemów FM:.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **FM: systemy**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **FM: systemy**.

    ![Link FM: Systems na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-fmsystems-test-user"></a>Tworzenie użytkownika testowego FM: Systems

1. W oknie przeglądarki sieci Web Zaloguj się do witryny firmy UKF: system jako administrator.

2. Przejdź do pozycji **Administracja systemu \> Zarządzaj \> \> Lista użytkowników użytkowników zabezpieczeń**.
   
    ![Administrowanie systemami](./media/fm-systems-tutorial/ic795905.png "Administrowanie systemami")

3. Kliknij pozycję **Utwórz nowego użytkownika**.
   
    ![Utwórz nowego użytkownika](./media/fm-systems-tutorial/ic795906.png "Utwórz nowego użytkownika")

4. W sekcji **Create User** (Tworzenie użytkownika) wykonaj następujące kroki:
   
    ![Tworzenie użytkownika](./media/fm-systems-tutorial/ic795907.png "Utwórz użytkownika")
   
    a. Wpisz **nazwę użytkownika**, **hasło**, **Potwierdź hasło**, **adres E-mail** i **identyfikator pracownika** dla prawidłowego konta Azure Active Directory, które chcesz udostępnić do powiązanych pól tekstowych.
   
    b. Kliknij przycisk **Dalej**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka FM: Systems w panelu dostępu należy automatycznie zalogować się do systemu FM: systemy, dla których skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)