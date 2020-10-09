---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą KnowledgeOwl | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i KnowledgeOwl.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.openlocfilehash: fc6cb5d992d4c961e23034560a688c7090622359
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91858102"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-knowledgeowl"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą KnowledgeOwl

W tym samouczku dowiesz się, jak zintegrować usługę KnowledgeOwl z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi KnowledgeOwl z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do KnowledgeOwl.
* Zezwól użytkownikom na automatyczne logowanie się do usługi KnowledgeOwl przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) KnowledgeOwl.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* KnowledgeOwl obsługuje usługę **SP i dostawcy tożsamości** zainicjowano Logowanie jednokrotne
* KnowledgeOwl obsługuje Inicjowanie obsługi użytkowników **just in Time**

## <a name="adding-knowledgeowl-from-the-gallery"></a>Dodawanie KnowledgeOwl z galerii

Aby skonfigurować integrację programu KnowledgeOwl z usługą Azure AD, musisz dodać KnowledgeOwl z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **KnowledgeOwl** w polu wyszukiwania.
1. Wybierz pozycję **KnowledgeOwl** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-knowledgeowl"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla KnowledgeOwl

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą KnowledgeOwl przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w KnowledgeOwl.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą KnowledgeOwl, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-knowledgeowl-sso)** w usłudze KnowledgeOwl, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    * **[Utwórz użytkownika testowego KnowledgeOwl](#create-knowledgeowl-test-user)** , aby dysponować odpowiednikiem B. Simon w KnowledgeOwl, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **KnowledgeOwl** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: 
    
    ```http
    https://app.knowledgeowl.com/sp
    https://app.knowledgeowl.com/sp/id/<unique ID>
    ```

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: 
    
    ```http
    https://subdomain.knowledgeowl.com/help/saml-login
    https://subdomain.knowledgeowl.com/docs/saml-login
    https://subdomain.knowledgeowl.com/home/saml-login
    https://privatedomain.com/help/saml-login
    https://privatedomain.com/docs/saml-login
    https://privatedomain.com/home/saml-login
    ```

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: 
    
    ```http
    https://subdomain.knowledgeowl.com/help/saml-login
    https://subdomain.knowledgeowl.com/docs/saml-login
    https://subdomain.knowledgeowl.com/home/saml-login
    https://privatedomain.com/help/saml-login
    https://privatedomain.com/docs/saml-login
    https://privatedomain.com/home/saml-login
    ```

    > [!NOTE]
    > Te wartości nie są prawdziwe. Musisz zaktualizować te wartości z rzeczywistego identyfikatora, adresu URL odpowiedzi i adresu URL Sign-On, co zostało wyjaśnione w dalszej części tego samouczka.

1. Aplikacja KnowledgeOwl oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image (obraz)](common/default-attributes.png)

1. Oprócz powyższych, aplikacja KnowledgeOwl oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je sprawdzić zgodnie z wymaganiami.

    | Nazwa | Atrybut źródłowy | Przestrzeń nazw |
    | ------------ | -------------------- | -----|
    | ssoid | user.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (RAW)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificateraw.png)

1. W sekcji **Konfigurowanie KnowledgeOwl** skopiuj odpowiednie adresy URL na podstawie wymagania.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi KnowledgeOwl.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **KnowledgeOwl**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-knowledgeowl-sso"></a>Konfigurowanie logowania jednokrotnego KnowledgeOwl

1. W innym oknie przeglądarki sieci Web Zaloguj się do firmowej witryny KnowledgeOwl jako administrator.

1. Kliknij pozycję **Ustawienia** , a następnie wybierz pozycję **zabezpieczenia**.

    ![Zrzut ekranu przedstawia zabezpieczenia wybrane w menu Ustawienia.](./media/knowledgeowl-tutorial/configure1.png)

1. Przewiń do **integracji SAML SSO** i wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia integrację protokołu SAML S O, w której można wprowadzić zmiany opisane tutaj.](./media/knowledgeowl-tutorial/configure2.png)

    a. Wybierz pozycję **Włącz logowanie jednokrotne SAML**.

    b. Skopiuj wartość **Identyfikator jednostki Sp** i wklej ją do **identyfikatora (identyfikator jednostki)** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

    c. Skopiuj wartość **adres URL logowania** do programu SP i wklej ją do pól tekstowych **adres URL logowania i adres URL odpowiedzi** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

    d. W polu tekstowym **dostawcy tożsamości entityID** wklej wartość **identyfikatora usługi Azure AD** , która została skopiowana z Azure Portal.

    e. W polu tekstowym **adres URL logowania dostawcy tożsamości** wklej wartość **adresu URL logowania** , która została skopiowana z Azure Portal.

    f. W polu tekstowym **adres URL wylogowywania dostawcy tożsamości** wklej wartość **adresu URL wylogowania** , która została skopiowana z Azure Portal

    przykład Przekaż pobrany certyfikat w Azure Portal przez kliknięcie przycisku **Przekaż certyfikat dostawcy tożsamości**.

    h. Kliknij pozycję **Mapuj atrybuty SAML** , aby mapować atrybuty, i wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia Mapowanie atrybutów SAML, w których można wprowadzać zmiany w tym miejscu.](./media/knowledgeowl-tutorial/configure3.png)

    * Wprowadź `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ssoid` do pola tekstowego **identyfikatora logowania jednokrotnego**
    * Wprowadź tekst `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` w polu tekstowym **username/email** .
    * Wprowadź tekst `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` w polu tekstowym **imię i nazwisko** .
    * Wprowadź tekst `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` w polu tekstowym **nazwisko** .
    * Kliknij pozycję **Zapisz**

    i. Kliknij przycisk **Zapisz** w dolnej części strony.

    ![Zrzut ekranu przedstawia przycisk Zapisz.](./media/knowledgeowl-tutorial/configure4.png)

### <a name="create-knowledgeowl-test-user"></a>Utwórz użytkownika testowego KnowledgeOwl

W tej sekcji użytkownik o nazwie B. Simon został utworzony w KnowledgeOwl. KnowledgeOwl obsługuje Inicjowanie obsługi użytkowników just in Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w usłudze KnowledgeOwl, zostanie utworzony nowy po uwierzytelnieniu.

> [!Note]
> Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej KnowledgeOwl](mailto:support@knowledgeowl.com).

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka KnowledgeOwl w panelu dostępu należy automatycznie zalogować się do KnowledgeOwl, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj KnowledgeOwl z usługą Azure AD](https://aad.portal.azure.com/)