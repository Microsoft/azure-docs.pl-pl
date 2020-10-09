---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą Workteam | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Workteam.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/19/2019
ms.author: jeedes
ms.openlocfilehash: c95fceaa90ca379519379d37c19fb15dcd1de321
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88546366"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workteam"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą Workteam

W tym samouczku dowiesz się, jak zintegrować usługę Workteam z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Workteam z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Workteam.
* Zezwól użytkownikom na automatyczne logowanie się do usługi Workteam przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) Workteam.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Workteam obsługuje usługę **SP i dostawcy tożsamości** zainicjowano Logowanie jednokrotne

## <a name="adding-workteam-from-the-gallery"></a>Dodawanie Workteam z galerii

Aby skonfigurować integrację programu Workteam z usługą Azure AD, musisz dodać Workteam z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Workteam** w polu wyszukiwania.
1. Wybierz pozycję **Workteam** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-workteam"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla Workteam

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Workteam przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Workteam.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Workteam, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-workteam-sso)** w usłudze Workteam, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego Workteam](#create-workteam-test-user)** , aby dysponować odpowiednikiem B. Simon w Workteam, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Workteam** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** aplikacja została wstępnie skonfigurowana w trybie inicjalizacji **dostawcy tożsamości** , a wymagane adresy URL są już wstępnie wypełnione na platformie Azure. Użytkownik musi zapisać konfigurację, klikając przycisk **Zapisz** .

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **adres URL logowania** wpisz adres URL:  `https://app.workte.am`

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie Workteam** skopiuj odpowiednie adresy URL na podstawie wymagania.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Workteam.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Workteam**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

### <a name="configure-workteam-sso"></a>Konfigurowanie logowania jednokrotnego Workteam

1. Aby zautomatyzować konfigurację w programie Workteam, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki kliknij pozycję **Setup Workteam** , aby skierować do aplikacji Workteam. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do usługi Workteam. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki 3-6.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować Workteam, Otwórz nowe okno przeglądarki sieci Web i zaloguj się w witrynie firmy Workteam jako administrator i wykonaj następujące czynności:

4. W prawym górnym rogu kliknij pozycję **logo profilu** , a następnie kliknij pozycję **Ustawienia organizacji**. 

    ![Ustawienia Workteam](./media/workteam-tutorial/tutorial_workteam_settings.png)

5. W obszarze **uwierzytelnianie** kliknij pozycję **logo ustawień**.

     ![Workteam Azure](./media/workteam-tutorial/tutorial_workteam_azure.png)

6. Na stronie **Ustawienia SAML** wykonaj następujące czynności:

     ![Workteam SAML](./media/workteam-tutorial/tutorial_workteam_saml.png)

    a. Wybierz pozycję **SAML dostawcy tożsamości** jako **usługę AD Azure**.

    b. W polu tekstowym **adres URL usługi SAML Single Sign-On** wklej wartość **adresu URL logowania**, która została skopiowana z Azure Portal.

    c. W polu tekstowym **Identyfikator jednostki SAML** wklej wartość **identyfikatora usługi Azure AD**, który został skopiowany z Azure Portal.

    d. W Notatniku otwórz **certyfikat z kodowaniem Base-64** pobrany z Azure Portal, skopiuj jego zawartość, a następnie wklej go do pola **certyfikatu podpisywania SAML (base64)** .

    e. Kliknij przycisk **OK**.

### <a name="create-workteam-test-user"></a>Utwórz użytkownika testowego Workteam

Aby umożliwić użytkownikom usługi Azure AD logowanie się w usłudze Workteam, muszą one być obsługiwane w usłudze Workteam. W Workteam, Inicjowanie obsługi jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do Workteam jako administrator zabezpieczeń.

2. W górnej części strony **Ustawienia organizacji** kliknij pozycję **Użytkownicy** , a następnie kliknij pozycję **nowy użytkownik**.

    ![Workteam użytkownika](./media/workteam-tutorial/tutorial_workteam_user.png)

3. Na stronie **nowy pracownik** wykonaj następujące czynności:

    ![Workteam nowy użytkownik](./media/workteam-tutorial/tutorial_workteam_newuser.png)

    a. W polu tekstowym **Nazwa** Wprowadź imię użytkownika, np **. B. Simon**.

    b. W polu tekstowym **Email** (Adres e-mail) wprowadź adres e-mail użytkownika, na przykład `B.Simon\@contoso.com`.

    c. Kliknij przycisk **OK**.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Workteam w panelu dostępu należy automatycznie zalogować się do Workteam, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj Workteam z usługą Azure AD](https://aad.portal.azure.com/)

