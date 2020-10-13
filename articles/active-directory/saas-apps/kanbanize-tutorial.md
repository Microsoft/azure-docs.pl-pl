---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą Kanbanize | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Kanbanize.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/09/2019
ms.author: jeedes
ms.openlocfilehash: af68100d0064187931ede43916d1ab3258baea38
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91850714"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-kanbanize"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą Kanbanize

W tym samouczku dowiesz się, jak zintegrować usługę Kanbanize z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Kanbanize z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Kanbanize.
* Zezwól użytkownikom na automatyczne logowanie się do usługi Kanbanize przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) Kanbanize.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Kanbanize obsługuje usługę **SP i dostawcy tożsamości** zainicjowano Logowanie jednokrotne
* Kanbanize obsługuje Inicjowanie obsługi użytkowników **just in Time**

## <a name="adding-kanbanize-from-the-gallery"></a>Dodawanie Kanbanize z galerii

Aby skonfigurować integrację programu Kanbanize z usługą Azure AD, musisz dodać Kanbanize z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Kanbanize** w polu wyszukiwania.
1. Wybierz pozycję **Kanbanize** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-kanbanize"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla Kanbanize

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Kanbanize przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Kanbanize.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Kanbanize, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-kanbanize-sso)** w usłudze Kanbanize, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego Kanbanize](#create-kanbanize-test-user)** , aby dysponować odpowiednikiem B. Simon w Kanbanize, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Kanbanize** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

     a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://<subdomain>.kanbanize.com/`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.kanbanize.com/saml/acs`

    c. Kliknij pozycję **Ustaw dodatkowe adresy URL**.

    d. W polu tekstowym **Stan przekazywania** wpisz adres URL: `/ctrl_login/saml_login`

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.kanbanize.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z [zespołem obsługi klienta Kanbanize](mailto:support@ms.kanbanize.com) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja Kanbanize oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Na poniższym zrzucie ekranu przedstawiono listę atrybutów domyślnych, gdzie atrybut nameidentifier jest mapowany na atrybut **user.userprincipalname**. Aplikacja Kanbanize oczekuje, że NameIdentifier mają być mapowane przy użyciu elementu **User. mail**, dlatego należy edytować Mapowanie atrybutów, klikając ikonę Edytuj i zmieniając mapowanie atrybutu.

    ![image (obraz)](common/edit-attribute.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie Kanbanize** skopiuj odpowiednie adresy URL na podstawie wymagania.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Kanbanize.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Kanbanize**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-kanbanize-sso"></a>Konfigurowanie logowania jednokrotnego Kanbanize

1. Aby zautomatyzować konfigurację w programie Kanbanize, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki kliknij pozycję **Skonfiguruj** , aby przekierować użytkownika do aplikacji Kanbanize. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do usługi Kanbanize. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i zautomatyzuje kroki 3–7.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować Kanbanize, Otwórz nowe okno przeglądarki sieci Web i zaloguj się w witrynie firmy Kanbanize jako administrator i wykonaj następujące czynności:

4. Przejdź do prawego górnego rogu strony, kliknij pozycję logo **Settings (ustawienia** ).

    ![Ustawienia Kanbanize](./media/kanbanize-tutorial/tutorial-kanbanize-set.png)

5. Na stronie Panel administracji z lewej strony menu kliknij pozycję **integracje** , a następnie Włącz **Logowanie jednokrotne**.

    ![Zrzut ekranu przedstawia panel Administracja z wybraną integracją.](./media/kanbanize-tutorial/tutorial-kanbanize-admin.png)

6. W obszarze integracje kliknij pozycję **Konfiguruj** , aby otworzyć stronę **integracja z pojedynczym Sign-On** .

    ![Konfiguracja Kanbanize](./media/kanbanize-tutorial/tutorial-kanbanize-config.png)

7. Na stronie **integracja o pojedynczej Sign-On** w obszarze **konfiguracje**wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia stronę integracja z pojedynczym Sign-On, na której wprowadzane są wartości w tym kroku.](./media/kanbanize-tutorial/tutorial-kanbanize-save.png)

    a. W polu tekstowym **Identyfikator jednostki dostawcy tożsamości** wklej wartość **identyfikatora usługi Azure AD**, która została skopiowana z Azure Portal.

    b. W polu tekstowym **punkt końcowy logowania dostawcy tożsamości** wklej wartość **adresu URL logowania**, która została skopiowana z Azure Portal.

    c. W polu tekstowym **punkt końcowy wylogowania dostawcy tożsamości** wklej wartość **adresu URL wylogowania**, która została skopiowana z Azure Portal.

    d. W polu tekstowym **nazwa atrybutu dla wiadomości e-mail** wprowadź tę wartość `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    e. W polu tekstowym **nazwa atrybutu dla pierwszej nazwy** wprowadź tę wartość `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    f. W polu **nazwa atrybutu dla** pola tekstowego nazwisko wprowadź tę wartość `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    > [!Note]
    > Możesz uzyskać te wartości, łącząc przestrzeń nazw i wartości nazw odpowiedniego atrybutu z sekcji atrybuty użytkownika w Azure Portal.

    przykład W Notatniku otwórz certyfikat zakodowany Base-64, który został pobrany z Azure Portal, skopiuj jego zawartość (bez znaczników początkowych i końcowych), a następnie wklej go w polu **certyfikat dostawcy tożsamości X. 509** .

    h. Zaznacz pole wyboru **Włącz logowanie przy użyciu logowania JEDNOkrotnego i Kanbanize**.

    i. Kliknij przycisk **Zapisz ustawienia**.

### <a name="create-kanbanize-test-user"></a>Utwórz użytkownika testowego Kanbanize

W tej sekcji użytkownik o nazwie B. Simon został utworzony w Kanbanize. Kanbanize obsługuje Inicjowanie obsługi użytkowników just in Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w usłudze Kanbanize, zostanie utworzony nowy po uwierzytelnieniu. Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem obsługi klienta Kanbanize](mailto:support@ms.kanbanize.com).

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Kanbanize w panelu dostępu należy automatycznie zalogować się do Kanbanize, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj Kanbanize z usługą Azure AD](https://aad.portal.azure.com/)

