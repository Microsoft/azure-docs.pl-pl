---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą Teamphoria | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Teamphoria.
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
ms.openlocfilehash: 30971c9c5280bfba37f46aaab0aaf2c2506502c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88542347"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-teamphoria"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą Teamphoria

W tym samouczku dowiesz się, jak zintegrować usługę Teamphoria z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Teamphoria z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Teamphoria.
* Zezwól użytkownikom na automatyczne logowanie się do usługi Teamphoria przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) Teamphoria.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Teamphoria obsługuje logowanie jednokrotne w usłudze **SP**

## <a name="adding-teamphoria-from-the-gallery"></a>Dodawanie Teamphoria z galerii

Aby skonfigurować integrację programu Teamphoria z usługą Azure AD, musisz dodać Teamphoria z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Teamphoria** w polu wyszukiwania.
1. Wybierz pozycję **Teamphoria** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-teamphoria"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla Teamphoria

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Teamphoria przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Teamphoria.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Teamphoria, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-teamphoria-sso)** w usłudze Teamphoria, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego Teamphoria](#create-teamphoria-test-user)** , aby dysponować odpowiednikiem B. Simon w Teamphoria, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Teamphoria** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<sub-domain>.teamphoria.com/login`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z [zespołem obsługi klienta Teamphoria](https://www.teamphoria.com/) , aby uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie Teamphoria** skopiuj odpowiednie adresy URL na podstawie wymagania.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Teamphoria.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Teamphoria**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-teamphoria-sso"></a>Konfigurowanie logowania jednokrotnego Teamphoria

1. Aby zautomatyzować konfigurację w programie Teamphoria, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki kliknij pozycję **Skonfiguruj** , aby przekierować użytkownika do aplikacji Teamphoria. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do usługi Teamphoria. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki 3-6.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować Teamphoria, Otwórz nowe okno przeglądarki sieci Web i zaloguj się w witrynie firmy Teamphoria jako administrator i wykonaj następujące czynności:

4. Przejdź do opcji **Ustawienia administratora** na pasku narzędzi po lewej stronie, a następnie na karcie Konfiguracja kliknij pozycję Logowanie **jednokrotne, aby otworzyć** okno Konfiguracja logowania jednokrotnego.

    ![Konfigurowanie logowania jednokrotnego](./media/teamphoria-tutorial/admin_sso_configure.png)

5. Kliknij opcję **Dodaj nową tożsamość dostawcy** w prawym górnym rogu, aby otworzyć formularz służący do dodawania ustawień logowania jednokrotnego.

    ![Konfigurowanie logowania jednokrotnego](./media/teamphoria-tutorial/add_new_identity_provider.png)

6. Wprowadź szczegóły w polach zgodnie z opisem poniżej.

    ![Konfigurowanie logowania jednokrotnego](./media/teamphoria-tutorial/Teamphoria_sso_save.png)

    a. **Nazwa wyświetlana**: Wprowadź nazwę wyświetlaną wtyczki na stronie administratora.

    b. **Nazwa przycisku**: Nazwa karty, która będzie wyświetlana na stronie logowania na potrzeby logowania za pomocą logowania jednokrotnego.

    c. **Certyfikat**: Otwórz certyfikat pobrany wcześniej z Azure Portal w Notatniku, skopiuj zawartość tego samego i wklej ją w tym miejscu w polu.

    d. **Punkt wejścia**: wklej **adres URL logowania** skopiowany wcześniej z Azure Portal.

    e. Przełącz opcję na wartość **włączone** , a następnie kliknij pozycję **Zapisz**.

### <a name="create-teamphoria-test-user"></a>Utwórz użytkownika testowego Teamphoria

Aby umożliwić użytkownikom usługi Azure AD logowanie się w usłudze Teamphoria, muszą one być obsługiwane w Teamphoria. W przypadku Teamphoria, Inicjowanie obsługi administracyjnej jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do firmowej witryny Teamphoria jako administrator.

1. Kliknij pozycję Ustawienia **administratora** na pasku narzędzi po lewej stronie, a następnie na karcie **Zarządzanie** kliknij pozycję **Użytkownicy** , aby otworzyć stronę administrator dla użytkowników.

    ![Dodawanie pracownika](./media/teamphoria-tutorial/admin_manage_users.png)

1. Kliknij opcję **zapraszanie ręczne** .

    ![Zapraszanie osób](./media/teamphoria-tutorial/admin_manage_add_users.png)

1. Na tej stronie wykonaj poniższą akcję.

    ![Zapraszanie osób](./media/teamphoria-tutorial/manual_user_invite.png)

    a. W polu tekstowym **adres e-mail** wprowadź **adres e-mail** użytkownika, na przykład B. Simon.

    b. W polu tekstowym **imię i nazwisko** Wprowadź imię użytkownika, np. **B**.

    c. W polu **tekstowym nazwisko Wprowadź nazwisko użytkownika** , np. **Simon**.

    d. Kliknij pozycję **Zaproś 1 użytkownika**. Użytkownik musi zaakceptować zaproszenie do utworzenia w systemie.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Teamphoria w panelu dostępu należy automatycznie zalogować się do Teamphoria, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj Teamphoria z usługą Azure AD](https://aad.portal.azure.com/)

