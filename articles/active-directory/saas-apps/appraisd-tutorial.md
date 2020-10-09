---
title: 'Samouczek: integracja Azure Active Directory z usługą Appraisd | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Appraisd.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/27/2019
ms.author: jeedes
ms.openlocfilehash: 2e355241191a4bdcab12c7c0b6527706b044a70c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91714477"
---
# <a name="tutorial-integrate-appraisd-with-azure-active-directory"></a>Samouczek: integracja Appraisd z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować usługę Appraisd z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Appraisd z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Appraisd.
* Zezwól użytkownikom na automatyczne logowanie się do usługi Appraisd przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) Appraisd.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym. Usługa Appraisd obsługuje funkcję SSO zainicjowaną przez usługę **SP i dostawcy tożsamości** .

## <a name="adding-appraisd-from-the-gallery"></a>Dodawanie aplikacji Appraisd z galerii

Aby skonfigurować integrację aplikacji Appraisd z usługą Azure AD, należy z poziomu galerii dodać tę aplikację do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Appraisd** w polu wyszukiwania.
1. Wybierz pozycję **Appraisd** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Appraisd przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Appraisd.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Appraisd, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj Appraisd](#configure-appraisd)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** , aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
4. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego Appraisd](#create-appraisd-test-user)** , aby miał odpowiednik B. Simon w Appraisd, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Przetestuj Logowanie jednokrotne](#test-sso)** , aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Appraisd** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** kliknij ikonę Edytuj/pióra, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja języka SAML** aplikacja została wstępnie skonfigurowana i wymagane adresy URL są już wstępnie wypełnione na platformie Azure. Użytkownik musi zapisać konfigurację, klikając przycisk Zapisz i wykonując następujące czynności:

    a. Kliknij pozycję **Ustaw dodatkowe adresy URL**.

    b. W polu tekstowym **Stan przekaźnika** wpisz adres URL: `<TENANTCODE>`

    c. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **dostawcę usług**, w polu tekstowym **Adres URL logowania** wpisz adres URL przy użyciu następującego wzorca: `https://app.appraisd.com/saml/<TENANTCODE>`

    > [!NOTE]
    > Uzyskasz rzeczywisty adres URL logowania i wartość stanu przekaźnika na stronie konfiguracji logowania jednokrotnego aplikacji Appraisd, co zostało wyjaśnione w dalszej części tego samouczka.

1. Aplikacja Appraisd oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Na poniższym zrzucie ekranu przedstawiono listę atrybutów domyślnych, gdzie atrybut **nameidentifier** jest mapowany na atrybut **user.userprincipalname**. Aplikacja Appraisd oczekuje, że **NameIdentifier** mają być mapowane przy użyciu elementu **User. mail**, dlatego należy edytować Mapowanie atrybutów, klikając ikonę **Edytuj** i zmieniając mapowanie atrybutu.

    ![Zrzut ekranu przedstawia okienko atrybuty użytkownika z wyróżnioną ikoną Edytuj.](common/edit-attribute.png)

1. Na stronie **Konfigurowanie pojedynczego Sign-On za pomocą języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

   ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie Appraisd** skopiuj odpowiednie adresy URL na podstawie wymagania.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-appraisd"></a>Konfigurowanie Appraisd

1. Aby zautomatyzować konfigurację w programie Appraisd, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki kliknij pozycję **Setup Appraisd** , aby skierować do aplikacji Appraisd. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do usługi Appraisd. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i zautomatyzuje kroki 3–7.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować Appraisd, Otwórz nowe okno przeglądarki sieci Web i zaloguj się w witrynie firmy Appraisd jako administrator i wykonaj następujące czynności:

4. W prawym górnym rogu strony kliknij ikonę **Ustawienia** , a następnie przejdź do **konfiguracji**.

    ![Zrzut ekranu przedstawia łącze konfiguracji o nazwie wychodzące.](./media/appraisd-tutorial/tutorial_appraisd_sett.png)

5. Po lewej stronie menu kliknij pozycję **SAML single sign-on** (Logowanie jednokrotne SAML).

    ![Zrzut ekranu przedstawia opcje konfiguracji z wyróżnioną opcją protokołu SAML logowania jednokrotnego.](./media/appraisd-tutorial/tutorial_appraisd_single.png)

6. Na stronie **SAML 2.0 Single Sign-On configuration** (Konfiguracja logowania jednokrotnego SAML 2.0) wykonaj następujące kroki:

    ![Zrzut ekranu przedstawia jednoSign-Onową stronę konfiguracji protokołu SAML 2,0, na której można edytować domyślny stan przekazywania i logowanie zainicjowane przez usługę U R L.](./media/appraisd-tutorial/tutorial_appraisd_saml.png)

    a. Skopiuj **domyślną wartość stanu przekazywania** i wklej ją w polu tekstowym **Stan przekazywania** w **podstawowej konfiguracji SAML** na Azure Portal.

    b. Skopiuj wartość **adresu URL logowania zainicjowaną przez usługę** i wklej ją w polu tekstowym **adres URL** logowania w **podstawowej konfiguracji SAML** na Azure Portal.

7. Przewiń tę samą stronę do pozycji **Identifying users** (Identyfikowanie użytkowników) i wykonaj następujące kroki:

    ![Zrzut ekranu przedstawia Identyfikowanie użytkowników, w których można wprowadzać wartości z tego kroku.](./media/appraisd-tutorial/tutorial_appraisd_identifying.png)

    a. W polu tekstowym **Identity Provider Single Sign-On URL** (Adres URL logowania jednokrotnego dostawcy tożsamości) wklej wartość **Adres URL logowania** skopiowaną z witryny Azure Portal, a następnie kliknij przycisk **Save** (Zapisz).

    b. W polu tekstowym **adres URL wystawcy dostawcy tożsamości** wklej wartość **identyfikatora usługi Azure AD**, która została skopiowana z Azure Portal i kliknij przycisk **Zapisz**.

    c. W programie Notepad Otwórz certyfikat zakodowany Base-64, który został pobrany z Azure Portal, skopiuj jego zawartość, a następnie wklej go w polu **certyfikat X. 509** , a następnie kliknij przycisk **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B. Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B. Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Appraisd.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz **Appraisd**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

### <a name="create-appraisd-test-user"></a>Tworzenie użytkownika testowego aplikacji Appraisd

Aby umożliwić użytkownikom usługi Azure AD logowanie się w aplikacji Appraisd, należy ich aprowizować w tej aplikacji. W aplikacji Appraisd aprowizowanie jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do aplikacji Appraisd jako administrator zabezpieczeń.

2. W prawym górnym rogu strony kliknij ikonę **Ustawienia** , a następnie przejdź do **Centrum administracyjnego**.

    ![Zrzut ekranu przedstawia opcje ustawień, w których można wybrać Centrum administracyjne.](./media/appraisd-tutorial/tutorial_appraisd_admin.png)

3. Na pasku narzędzi u góry strony kliknij pozycję **osoby**, a następnie przejdź do **obszaru Dodaj nowego użytkownika**.

    ![Zrzut ekranu przedstawia stronę Appraisd z osobami i Dodaj nowego użytkownika o nazwie.](./media/appraisd-tutorial/tutorial_appraisd_user.png)

4. Na stronie **Add a new user** (Dodawanie nowego użytkownika) wykonaj następujące kroki:

    ![Zrzut ekranu przedstawia stronę Dodawanie nowego użytkownika.](./media/appraisd-tutorial/tutorial_appraisd_newuser.png)

    a. W polu tekstowym **imię i nazwisko** Wprowadź imię użytkownika, np. **Britta**.

    b. W polu tekstowym **nazwisko** Wprowadź nazwisko użytkownika, np. **Simon**.

    c. W polu tekstowym **Email** (Adres e-mail) wprowadź adres e-mail użytkownika, na przykład `B. Simon@contoso.com`.

    d. Kliknij pozycję **Dodaj użytkownika**.

### <a name="test-sso"></a>Testuj Logowanie jednokrotne

Po wybraniu kafelka Appraisd w panelu dostępu należy automatycznie zalogować się do Appraisd, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
