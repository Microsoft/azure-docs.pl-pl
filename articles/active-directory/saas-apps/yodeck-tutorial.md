---
title: 'Samouczek: integracja Azure Active Directory z usługą Yodeck | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Yodeck.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: 68e961336d7e86f9983fea49044137894ab0f985
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92895146"
---
# <a name="tutorial-azure-active-directory-integration-with-yodeck"></a>Samouczek: integracja Azure Active Directory z usługą Yodeck

W tym samouczku dowiesz się, jak zintegrować usługę Yodeck z usługą Azure Active Directory (Azure AD).
Integracja Yodeck z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do usługi Yodeck.
* Możesz pozwolić użytkownikom na automatyczne logowanie do Yodeck (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą Yodeck, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym w Yodeck

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Yodeck obsługuje usługę **SP** i **dostawcy tożsamości** zainicjowano Logowanie jednokrotne

## <a name="adding-yodeck-from-the-gallery"></a>Dodawanie Yodeck z galerii

Aby skonfigurować integrację programu Yodeck z usługą Azure AD, musisz dodać Yodeck z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Yodeck z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Yodeck**, wybierz pozycję **Yodeck** from panel wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

     ![Yodeck na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą Yodeck na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Yodeck.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi Yodeck, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie](#configure-yodeck-single-sign-on)** jednokrotne w usłudze Yodeck, aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego Yodeck](#create-yodeck-test-user)** , aby uzyskać odpowiednik Britta Simon w Yodeck, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą Yodeck, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Yodeck** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wykonaj następujące czynności:

    ![Yodeck domenę i adresy URL Logowanie jednokrotne](common/idp-identifier.png)

    W polu tekstowym **Identyfikator** wpisz adres URL: `https://app.yodeck.com/api/v1/account/metadata/`

5. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    ![Zrzut ekranu przedstawia podstawową konfigurację języka SAML przy użyciu linku Przekaż plik metadanych.](common/both-preintegrated-signon.png)

    W polu tekstowym **adres URL logowania** wpisz adres URL:  `https://app.yodeck.com/login`

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij przycisk kopiowania, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

### <a name="configure-yodeck-single-sign-on"></a>Konfigurowanie pojedynczego Sign-On Yodeck

1. Aby zautomatyzować konfigurację w programie **Yodeck**, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Zrzut ekranu przedstawia przycisk Zainstaluj rozszerzenie.](./media/target-process-tutorial/install_extension.png)

1. Po dodaniu rozszerzenia do przeglądarki kliknij pozycję **Setup Yodeck** , aby skierować do aplikacji Yodeck. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do usługi Yodeck. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki 3-5.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

    **Jeśli chcesz skonfigurować aplikację ręcznie, wykonaj następujące kroki:**

1. W innym oknie przeglądarki sieci Web Zaloguj się do firmowej witryny Yodeck jako administrator.

1. Kliknij opcję **Ustawienia użytkownika** formularz w prawym górnym rogu strony i wybierz pozycję **Ustawienia konta**.

    ![Zrzut ekranu przedstawia ustawienia konta wybrane dla użytkownika.](./media/yodeck-tutorial/configure1.png)

1. Wybierz pozycję **SAML** i wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia kartę SAML, w której można wykonać te kroki.](./media/yodeck-tutorial/configure2.png)

    a. Wybierz pozycję **Importuj z adresu URL**.

    b. W polu tekstowym **adres URL** wklej wartość **adresu URL metadanych federacji aplikacji** , która została skopiowana z Azure Portal i kliknij przycisk **Importuj**.
    
    c. Po zaimportowaniu **adresu URL metadanych federacji aplikacji** pozostałe pola są wypełniane automatycznie.

    d. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz brittasimon@yourcompanydomain.extension . Na przykład BrittaSimon@contoso.com

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji Britta Simon do korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Yodeck.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **Yodeck**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **Yodeck**.

    ![Link Yodeck na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-yodeck-test-user"></a>Utwórz użytkownika testowego Yodeck

Aby umożliwić użytkownikom usługi Azure AD logowanie się w usłudze Yodeck, muszą one być obsługiwane w usłudze Yodeck. W przypadku Yodeck, Inicjowanie obsługi administracyjnej jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do firmowej witryny Yodeck jako administrator.

2. Kliknij opcję **Ustawienia użytkownika** formularz w prawym górnym rogu strony i wybierz pozycję **Użytkownicy**.

    ![Zrzut ekranu przedstawiający użytkowników wybranych dla danego użytkownika.](./media/yodeck-tutorial/user1.png)

3. Kliknij pozycję **+ użytkownik** , aby otworzyć kartę **szczegóły użytkownika** .

    ![Zrzut ekranu przedstawia przycisk Użytkownicy.](./media/yodeck-tutorial/user2.png)

4. Na stronie okna dialogowego **User Details (Szczegóły użytkownika)** wykonaj następujące kroki:

    ![Zrzut ekranu przedstawia kartę Szczegóły użytkownika, na której można wykonać te kroki.](./media/yodeck-tutorial/user3.png)

    a. W polu tekstowym **imię i nazwisko** wpisz imię użytkownika, np. **Britta**.

    b. W polu tekstowym **Last Name** (Nazwisko) wpisz nazwisko użytkownika, takie jak **Simon**.

    c. W polu tekstowym **Email** (Adres e-mail) wpisz adres e-mail użytkownika, np. brittasimon@contoso.com.

    d. Wybierz opcję odpowiednie **uprawnienia konta** zgodnie z wymaganiami organizacji.
    
    e. Kliknij pozycję **Zapisz**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Yodeck w panelu dostępu należy automatycznie zalogować się do Yodeck, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)