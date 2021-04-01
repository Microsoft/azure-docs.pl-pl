---
title: 'Samouczek: integracja Azure Active Directory z usługą dmarcian | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją dmarcian.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/01/2019
ms.author: jeedes
ms.openlocfilehash: 4a5ea47352bfbe86fe194821da0b6bf10818caf9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92454732"
---
# <a name="tutorial-integrate-dmarcian-with-azure-active-directory"></a>Samouczek: integracja dmarcian z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować usługę dmarcian z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi dmarcian z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do dmarcian.
* Zezwól użytkownikom na automatyczne logowanie się do usługi dmarcian przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* subskrypcja z włączonym logowaniem jednokrotnym (SSO) dmarcian.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja dmarcian obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług oraz dostawcę tożsamości**

## <a name="adding-dmarcian-from-the-gallery"></a>Dodawanie aplikacji dmarcian z galerii

Aby skonfigurować integrację aplikacji dmarcian z usługą Azure AD, musisz dodać aplikację dmarcian z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **dmarcian** w polu wyszukiwania.
1. Wybierz pozycję **dmarcian** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą dmarcian przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w dmarcian.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą dmarcian, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj Logowanie jednokrotne](#configure-dmarcian-sso)** w usłudze dmarcian, aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
5. **[Utwórz użytkownika testowego dmarcian](#create-dmarcian-test-user)** , aby dysponować odpowiednikiem B. Simon w dmarcian, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
6. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Dmarcian** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** kliknij ikonę Edytuj/pióra, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wykonaj następujące czynności:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: 

    ```http
    https://us.dmarcian.com/sso/saml/<ACCOUNT_ID>/sp.xml
    https://dmarcian-eu.com/sso/saml/<ACCOUNT_ID>/sp.xml
    https://dmarcian-ap.com/sso/saml/<ACCOUNT_ID>/sp.xml
    ```

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: 

    ```http
    https://us.dmarcian.com/login/<ACCOUNT_ID>/handle/
    https://dmarcian-eu.com/login/<ACCOUNT_ID>/handle/
    https://dmarcian-ap.com/login/<ACCOUNT_ID>/handle/
    ```

5. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: 
    
    ```http
    https://us.dmarcian.com/login/<ACCOUNT_ID>
    https://dmarcian-eu.com/login/<ACCOUNT_ID>
    https://dmarciam-ap.com/login/<ACCOUNT_ID>
    ```
     
    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zaktualizujesz te wartości, używając rzeczywistego identyfikatora, adresu URL odpowiedzi i adresu URL logowania, co zostało opisane w dalszej części tego samouczka.

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij przycisk kopiowania, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

### <a name="configure-dmarcian-sso"></a>Konfigurowanie logowania jednokrotnego dmarcian

1. Aby zautomatyzować konfigurację w programie dmarcian, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki kliknij pozycję **Setup dmarcian** , aby skierować do aplikacji dmarcian. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do usługi dmarcian. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki 3-6.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować dmarcian, Otwórz nowe okno przeglądarki sieci Web i zaloguj się w witrynie firmy dmarcian jako administrator i wykonaj następujące czynności:

4. Kliknij pozycję **Profile** (Profil) w prawym górnym rogu, a następnie przejdź do obszaru **Preferences** (Preferencje).

    ![Preferencje](./media/dmarcian-tutorial/tutorial_dmarcian_pref.png)

5. Przewiń w dół i kliknij sekcję **Single Sign-On** (Logowanie jednokrotne), a następnie kliknij przycisk **Configure** (Konfiguruj).

    ![Logowanie jednokrotne](./media/dmarcian-tutorial/tutorial_dmarcian_sso.png)

6. Na stronie **SAML Single Sign-On** (Logowanie jednokrotne języka SAML) ustaw pozycję **Status** (Stan) na **Enabled** (Włączono) i wykonaj następujące kroki:

    ![Uwierzytelnianie](./media/dmarcian-tutorial/tutorial_dmarcian_auth.png)

    * W sekcji **Add dmarcian to your Identity Provider** (Dodawanie aplikacji dmarcian do dostawcy tożsamości) kliknij pozycję **COPY** (Kopiuj), aby skopiować **adres URL usługi Assertion Consumer Service** dla wystąpienia i wkleić go w polu tekstowym **Adres URL odpowiedzi** w **sekcji Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

    * W sekcji **Add dmarcian to your Identity Provider** (Dodawanie aplikacji dmarcian do dostawcy tożsamości) kliknij pozycję **COPY** (Kopiuj), aby skopiować **identyfikator jednostki** dla wystąpienia i wkleić go w polu tekstowym **Identyfikator** w **sekcji Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

    * W sekcji **Set up Authentication** (Konfigurowanie uwierzytelniania) w polu tekstowym **Identity Provider Metadata** (Metadane dostawcy tożsamości) wklej **adres URL metadanych federacyjnych aplikacji** skopiowany z witryny Azure Portal.

    * W sekcji **Set up Authentication** (Konfigurowanie uwierzytelniania) w polu tekstowym **Attribute Statements** (Instrukcje atrybutów) wklej adres URL `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    * W sekcji **Set up Login URL** (Konfigurowanie adresu URL logowania) skopiuj **adres URL logowania** dla wystąpienia i wklej go w polu tekstowym **Adres URL logowania** w **sekcji Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

        > [!Note]
        > Możesz zmodyfikować **adres URL logowania** zgodnie z zasadami Twojej organizacji.

    * Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi dmarcian.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **dmarcian**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

### <a name="create-dmarcian-test-user"></a>Tworzenie użytkownika testowego aplikacji dmarcian

Aby umożliwić użytkownikom usługi Azure AD logowanie się do aplikacji dmarcian, należy ich aprowizować w tej aplikacji. W aplikacji dmarcian aprowizowanie jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do aplikacji dmarcian jako administrator zabezpieczeń.

2. Kliknij pozycję **Profile** (Profil) w górnym prawym rogu i przejdź do obszaru **Manage Users** (Zarządzanie użytkownikami).

    ![Użytkownik](./media/dmarcian-tutorial/tutorial_dmarcian_user.png)

3. Po prawej stronie sekcji **SSO Users** (Użytkownicy logowania jednokrotnego) kliknij pozycję **Add New User** (Dodaj nowego użytkownika).

    ![Dodawanie użytkownika](./media/dmarcian-tutorial/tutorial_dmarcian_addnewuser.png)

4. W wyskakującym okienku **Add New User** (Dodawanie nowego użytkownika) wykonaj następujące kroki:

    ![Nowy użytkownik](./media/dmarcian-tutorial/tutorial_dmarcian_save.png)

    a. W polu tekstowym **adres E-mail nowego użytkownika** wprowadź adres e-mail użytkownika, np. **brittasimon \@ contoso.com**.

    b. Jeśli chcesz nadać użytkownikowi uprawnienia administratora, zaznacz pole **Make User an Admin** (Oznacz użytkownika jako administratora).

    c. Kliknij pozycję **Dodaj użytkownika**.

### <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka dmarcian w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji dmarcian, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)