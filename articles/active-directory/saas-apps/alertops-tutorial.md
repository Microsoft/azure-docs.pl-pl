---
title: 'Samouczek: integracja Azure Active Directory z usługą AlertOps | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją AlertOps.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.openlocfilehash: 094b1a029c8e10abce3ca282de0cc99be34d313a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92318858"
---
# <a name="tutorial-integrate-alertops-with-azure-active-directory"></a>Samouczek: integracja AlertOps z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować usługę AlertOps z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi AlertOps z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do AlertOps.
* Zezwól użytkownikom na automatyczne logowanie się do usługi AlertOps przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) AlertOps.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym. Usługa AlertOps obsługuje funkcję SSO zainicjowaną przez usługę **SP i dostawcy tożsamości** .

## <a name="adding-alertops-from-the-gallery"></a>Dodawanie aplikacji AlertOps z galerii

Aby skonfigurować integrację aplikacji AlertOps z usługą Azure AD, należy z poziomu galerii dodać to rozwiązanie do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **AlertOps** w polu wyszukiwania.
1. Wybierz pozycję **AlertOps** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą AlertOps przy użyciu użytkownika testowego o nazwie **Britta Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w AlertOps.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą AlertOps, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj AlertOps](#configure-alertops)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** , aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą Britta Simon.
4. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby umożliwić usłudze Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego AlertOps](#create-alertops-test-user)** , aby uzyskać odpowiednik Britta Simon w AlertOps, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Przetestuj Logowanie jednokrotne](#test-sso)** , aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **AlertOps** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** kliknij ikonę Edytuj/pióra, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wykonaj następujące czynności:

    1. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://<SUBDOMAIN>.alertops.com`

    1. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<SUBDOMAIN>.alertops.com/login.aspx`

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<SUBDOMAIN>.alertops.com/login.aspx`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z [zespołem obsługi klienta aplikacji AlertOps](mailto:support@alertops.com) w celu uzyskania tych wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie pojedynczego Sign-On za pomocą języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

   ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie AlertOps** skopiuj odpowiednie adresy URL na podstawie wymagania.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-alertops"></a>Konfigurowanie AlertOps

1. Aby zautomatyzować konfigurację w programie AlertOps, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki kliknij pozycję **Setup AlertOps** , aby skierować do aplikacji AlertOps. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do usługi AlertOps. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki 3-5.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować AlertOps, Otwórz nowe okno przeglądarki sieci Web i zaloguj się w witrynie firmy AlertOps jako administrator i wykonaj następujące czynności:

4. Kliknij pozycję **Account settings** (Ustawienia konta) w panelu nawigacji po lewej stronie.

    ![Zrzut ekranu przedstawia menu AlertOps z ustawieniami konta o nazwie.](./media/alertops-tutorial/configure1.png)

5. Na stronie **Subscription Settings** (Ustawienia subskrypcji) wybierz pozycję **SSO** (Logowanie jednokrotne), a następnie wykonaj następujące kroki:

    ![Zrzut ekranu przedstawia okno Ustawienia subskrypcji dla S S o wartości wprowadzone w tym kroku.](./media/alertops-tutorial/configure2.png)

    a. Zaznacz pole wyboru **Użyj logowania jednokrotnego Sign-On (SSO)**.

    b. Wybierz **Azure Active Directory** jako **dostawcę rejestracji jednokrotnej** z listy rozwijanej.

    c. W polu tekstowym **adres URL wystawcy** Użyj wartości identyfikatora, która została użyta w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

    d. W polu tekstowym **adres URL punktu końcowego SAML** wklej wartość **adresu URL logowania** , która została skopiowana z Azure Portal.

    e. W polu tekstowym **adres URL punktu końcowego SLO** wklej wartość **adresu URL logowania** , która została skopiowana z Azure Portal.

    f. Z listy rozwijanej **SAML Signature Algorithm** (Algorytm podpisu SAML) wybierz pozycję **SHA256**.

    przykład Otwórz pobrany plik Certificate(Base64) w Notatniku. Skopiuj jego zawartość do schowka, a następnie wklej go w polu tekstowym X.509 Certificate (Certyfikat X.509).

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie Britta Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `Britta Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `BrittaSimon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę Britta Simon do korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do usługi AlertOps.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **AlertOps**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **Britta Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

### <a name="create-alertops-test-user"></a>Tworzenie użytkownika testowego aplikacji AlertOps

1. W innym oknie przeglądarki Zaloguj się do witryny firmy AlertOps jako administrator.

2. Kliknij pozycję **Users** (Użytkownicy) na panelu nawigacyjnym po lewej stronie.

    ![Zrzut ekranu przedstawia menu AlertOps z użytkownikami o nazwie out.](./media/alertops-tutorial/user1.png)

3. Wybierz pozycję **Dodaj użytkownika**.

    ![Zrzut ekranu przedstawia okno użytkownicy z przyciskiem Dodaj użytkownika.](./media/alertops-tutorial/user2.png)

4. W oknie dialogowym **Add User** (Dodawanie użytkownika) wykonaj następujące kroki:

    ![Zrzut ekranu przedstawia okienko Dodawanie użytkowników z wartościami wprowadzonymi w tym kroku.](./media/alertops-tutorial/user3.png)

    a. W polu tekstowym **Login User Name** (Nazwa użytkownika logowania) wprowadź nazwę użytkownika, na przykład **Brittasimon**.

    b. W polu tekstowym **Oficjalne wiadomości e-mail** wprowadź adres e-mail użytkownika, np. **Brittasimon \@ contoso.com**.

    c. W polu tekstowym **First Name** (Imię) wpisz imię użytkownika, na przykład **Britta**.

    d. W polu tekstowym **Last Name** (Nazwisko) wprowadź nazwisko użytkownika, na przykład **Simon**.

    e. Wybierz wartość z listy rozwijanej **Type** (Typ) zgodnie z wymaganiami w Twojej organizacji.

    f. Wybierz wartość z listy rozwijanej **Role** (Rola) zgodnie z wymaganiami w Twojej organizacji.

    przykład Wybierz pozycję **Dodaj**.

### <a name="test-sso"></a>Testuj Logowanie jednokrotne

Po wybraniu kafelka AlertOps w panelu dostępu należy automatycznie zalogować się do AlertOps, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)