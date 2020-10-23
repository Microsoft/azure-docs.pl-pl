---
title: 'Samouczek: integracja Azure Active Directory z pomocą techniczną Jitbit | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i pomocą techniczną Jitbit.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: a1f000572badcaa09c958c7ca19ed81c4d0603bb
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92459513"
---
# <a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>Samouczek: integracja Azure Active Directory z pomocą techniczną Jitbit

W tym samouczku dowiesz się, jak zintegrować pomoc techniczną Jitbit z usługą Azure Active Directory (Azure AD).
Integracja pomocy technicznej Jitbit z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do pomocy technicznej Jitbit.
* Możesz pozwolić użytkownikom na automatyczne logowanie do Jitbit pomocy technicznej (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z pomocą techniczną Jitbit, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja z włączonym logowaniem jednokrotnym w Jitbit pomocy technicznej

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Pomoc techniczna Jitbit obsługuje zainicjowane przez usługę **SP** SSO

## <a name="adding-jitbit-helpdesk-from-the-gallery"></a>Dodawanie pomocy technicznej Jitbit z galerii

Aby skonfigurować integrację pomocy technicznej Jitbit z usługą Azure AD, musisz dodać do listy zarządzanych aplikacji SaaS pomoc techniczną Jitbit z galerii.

**Aby dodać pomoc techniczną Jitbit z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz Jitbit Help (pomoc **techniczna**), wybierz pozycję **Pomoc techniczna** w panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

     ![Jitbit pomoc techniczną na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą pomocy technicznej Jitbit w oparciu o użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Jitbit.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą pomocy technicznej Jitbit, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. Skonfiguruj Logowanie jednokrotne w **[Jitbit pomocy technicznej](#configure-jitbit-helpdesk-single-sign-on)** , aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego pomocy technicznej Jitbit](#create-jitbit-helpdesk-test-user)** , aby dysponować odpowiednikiem Britta Simon w pomocy technicznej Jitbit, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą pomocy technicznej Jitbit, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **pomocy technicznej Jitbit** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje o rejestracji jednokrotnej w domenie i adresach URL Jitbit pomocy technicznej](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: 
    | |
    | ----------------------------------------|
    | `https://<hostname>/helpdesk/User/Login`|
    | `https://<tenant-name>.Jitbit.com`|
    | |
    
    > [!NOTE] 
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z [zespołem pomocy technicznej programu Jitbit](https://www.jitbit.com/support/) w celu uzyskania tej wartości.

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL w następujący sposób: `https://www.jitbit.com/web-helpdesk/`

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie pomocy technicznej Jitbit** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-jitbit-helpdesk-single-sign-on"></a>Konfigurowanie Sign-On pomocy technicznej Jitbit

1. W innym oknie przeglądarki sieci Web Zaloguj się do firmowej witryny pomocy technicznej Jitbit jako administrator.

1. Na pasku narzędzi u góry kliknij pozycję **Administracja**.

    ![Administracja](./media/jitbit-helpdesk-tutorial/ic777681.png "Administracja")

1. Kliknij pozycję **Ustawienia ogólne**.

    ![Zrzut ekranu przedstawia link Ustawienia ogólne.](./media/jitbit-helpdesk-tutorial/ic777680.png "Użytkownicy, firmy i uprawnienia")

1. W sekcji Konfiguracja **ustawień uwierzytelniania** wykonaj następujące czynności:

    ![Ustawienia uwierzytelniania](./media/jitbit-helpdesk-tutorial/ic777683.png "Ustawienia uwierzytelniania")

    a. Wybierz pozycję Włącz logowanie jednokrotne w protokole **SAML 2,0**, aby zalogować się przy użyciu jednego Sign-On (SSO) z **OneLogin**.

    b. W polu tekstowym **adres URL punktu końcowego** wklej wartość **adresu URL logowania** skopiowanego z Azure Portal.

    c. Otwórz certyfikat zakodowany w formacie **Base-64** w Notatniku, skopiuj zawartość tego pliku do schowka, a następnie wklej go do pola tekstowego **certyfikatu X. 509** .

    d. Kliknij przycisk **Zapisz zmiany**.

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

Ta sekcja umożliwia włączenie usługi Britta Simon w celu korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do pomocy technicznej Jitbit.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **Pomoc techniczna Jitbit**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **Pomoc techniczna Jitbit**.

    ![Link do pomocy technicznej Jitbit na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-jitbit-helpdesk-test-user"></a>Utwórz użytkownika testowego pomocy technicznej Jitbit

Aby umożliwić użytkownikom usługi Azure AD logowanie się do pomocy technicznej Jitbit, muszą one być obsługiwane w pomocy technicznej Jitbit. W przypadku pomocy technicznej Jitbit, Inicjowanie obsługi jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do dzierżawy **pomocy technicznej Jitbit** .

1. W menu u góry kliknij pozycję **Administracja**.

    ![Administracja](./media/jitbit-helpdesk-tutorial/ic777681.png "Administracja")

1. Kliknij pozycję **Użytkownicy, firmy i uprawnienia**.

    ![Użytkownicy, firmy i uprawnienia](./media/jitbit-helpdesk-tutorial/ic777682.png "Użytkownicy, firmy i uprawnienia")

1. Kliknij pozycję **Dodaj użytkownika**.

    ![Dodaj użytkownika](./media/jitbit-helpdesk-tutorial/ic777685.png "Dodaj użytkownika")

1. W sekcji Tworzenie wpisz dane konta usługi Azure AD, które chcesz udostępnić w następujący sposób:

    ![Utwórz](./media/jitbit-helpdesk-tutorial/ic777686.png "Utwórz")

   a. W polu tekstowym **Nazwa** użytkownika wpisz nazwę użytkownika, na przykład **BrittaSimon**.

   b. W polu tekstowym **adres e-mail** wpisz adres e-mail użytkownika **BrittaSimon@contoso.com** .

   c. W polu tekstowym **imię i nazwisko** wpisz imię użytkownika, np. **Britta**.

   d. W polu tekstowym **nazwisko** wpisz nazwisko użytkownika, np. **Simon**.

   e. Kliknij pozycję **Utwórz**.

> [!NOTE]
> Do aprowizacji kont użytkowników usługi Azure AD można używać innych narzędzi do tworzenia kont użytkowników i interfejsów API usługi Jitbit.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka pomocy technicznej Jitbit w panelu dostępu należy automatycznie zalogować się do pomocy technicznej Jitbit, dla której skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)