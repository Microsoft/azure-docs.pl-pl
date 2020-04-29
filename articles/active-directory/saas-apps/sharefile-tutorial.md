---
title: 'Samouczek: integracja Azure Active Directory z usługą Citrix ShareFile | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Citrix ShareFile.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: e14fc310-bac4-4f09-99ef-87e5c77288b6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71d2fe97a4af07f48038ffd88de648247b56bb80
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "73160035"
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>Samouczek: integracja Azure Active Directory z usługą Citrix ShareFile

Z tego samouczka dowiesz się, jak zintegrować aplikację Citrix ShareFile z usługą Azure Active Directory (Azure AD).
Integracja aplikacji Citrix ShareFile z usługą Azure AD oferuje następujące korzyści:

* W usłudze Azure AD możesz kontrolować, kto ma dostęp do aplikacji Citrix ShareFile.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do aplikacji Citrix ShareFile (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji usługi Azure AD z aplikacją Citrix ShareFile potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji Citrix ShareFile z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Citrix ShareFile obsługuje logowanie jednokrotne inicjowane przez **SP**

## <a name="adding-citrix-sharefile-from-the-gallery"></a>Dodawanie aplikacji Citrix ShareFile z galerii

Aby skonfigurować integrację aplikacji Citrix ShareFile z usługą Azure AD, musisz dodać aplikację Citrix ShareFile z galerii do swojej listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację Citrix ShareFile z galerii, wykonaj następujące kroki:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Citrix ShareFile**, wybierz pozycję **Citrix ShareFile** z panelu wyników i kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Aplikacja Citrix ShareFile na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z aplikacją Citrix ShareFile, korzystając z danych testowego użytkownika **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji Citrix ShareFile.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z aplikacją Citrix ShareFile, należy utworzyć poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w aplikacji Citrix ShareFile](#configure-citrix-sharefile-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego aplikacji Citrix ShareFile](#create-citrix-sharefile-test-user)** — aby mieć w aplikacji Citrix ShareFile odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w aplikacji Citrix ShareFile, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **Citrix ShareFile** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje o domenie i adresach URL logowania jednokrotnego aplikacji Citrix ShareFile](common/sp-identifier-reply.png)

    a. W polu tekstowym **adres URL logowania** wpisz adres URL, używając następującego wzorca:`https://<tenant-name>.sharefile.com/saml/login`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, korzystając z następującego wzorca:

    | |
    |---|
    | `https://<tenant-name>.sharefile.com`|
    | `https://<tenant-name>.sharefile.com/saml/info`|
    | `https://<tenant-name>.sharefile1.com/saml/info`|
    | `https://<tenant-name>.sharefile1.eu/saml/info`|
    | `https://<tenant-name>.sharefile.eu/saml/info`|
    | |
    
    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: 
    
    | |
    |---|
    | `https://<tenant-name>.sharefile.com/saml/acs`|
    | `https://<tenant-name>.sharefile.eu/saml/<URL path>`|
    | `https://<tenant-name>.sharefile.com/saml/<URL path>`|

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami adresu URL logowania, identyfikatora i adresu URL odpowiedzi. Skontaktuj się z [zespołem pomocy technicznej klienta Citrix ShareFile](https://www.citrix.co.in/products/citrix-content-collaboration/support.html), aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Skonfiguruj aplikację Citrix ShareFile** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-citrix-sharefile-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w aplikacji Citrix ShareFile

1. W innym oknie przeglądarki internetowej zaloguj się do firmowej witryny usługi **Citrix ShareFile** jako administrator.

2. Na pasku narzędzi u góry kliknij pozycję **Admin** (Administrator).

3. W okienku nawigacji po lewej stronie wybierz pozycję **Configure Single Sign-On** (Skonfiguruj logowanie jednokrotne).
   
    ![Administrowanie kontem](./media/sharefile-tutorial/ic773627.png "Administrowanie kontem")

4. Na stronie dialogowej **Single Sign-On / SAML 2.0 Configuration** (Logowanie jednokrotne / Konfiguracja SAML 2.0) w obszarze **Basic Settings** (Ustawienia podstawowe) wykonaj następujące kroki:
   
    ![Rejestracja jednokrotna](./media/sharefile-tutorial/ic773628.png "Rejestracja jednokrotna")
   
    a. Kliknij pozycję **Enable SAML** (Włącz protokół SAML).
    
    b. W polu tekstowym **Your IDP Issuer/ Entity ID** (Wystawca IDP / Identyfikator jednostki) wklej wartość **identyfikatora usługi Azure AD** skopiowaną z witryny Azure Portal.

    c. Kliknij pozycję **Change** (Zmień) obok pola **X.509 Certificate** (Certyfikat X.509), a następnie przekaż certyfikat pobrany z witryny Azure Portal.
    
    d. W polu tekstowym **Login URL** (Adres URL logowania) wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.
    
    e. W polu tekstowym **adres URL wylogowywania** wklej wartość **adresu URL wylogowywania** skopiowanego z Azure Portal.

5. Kliknij pozycję **Save** (Zapisz) w portalu zarządzania usługi Citrix ShareFile.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon\@yourcompanydomain. Extension**  
    Na przykład: BrittaSimon@contoso.com

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz możliwość logowania jednokrotnego na platformie Azure dla użytkownika Britta Simon, udzielając dostępu do aplikacji Citrix ShareFile.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję **Wszystkie aplikacje**, a następnie wybierz pozycję **Citrix ShareFile**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wpisz i wybierz ciąg **Citrix ShareFile**.

    ![Link aplikacji Citrix ShareFile na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-citrix-sharefile-test-user"></a>Tworzenie użytkownika testowego aplikacji Citrix ShareFile

Aby umożliwić użytkownikom usługi Azure AD logowanie się do aplikacji Citrix ShareFile, należy ich aprowizować w aplikacji Citrix ShareFile. W przypadku aplikacji Citrix ShareFile aprowizację wykonuje się ręcznie.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do dzierżawy usługi **Citrix ShareFile**.

2. Kliknij pozycję **Manage Users \> Manage Users Home \> + Create Employee** (Zarządzaj użytkownikami > Zarządzaj użytkownikami — strona główna > + Utwórz pracownika).
   
    ![Utwórz pracownika](./media/sharefile-tutorial/IC781050.png "Utwórz pracownika")

3. W sekcji **Basic Information** (Informacje podstawowe) wykonaj następujące kroki:
   
    ![Informacje podstawowe](./media/sharefile-tutorial/IC799951.png "Informacje podstawowe")
   
    a. W polu tekstowym **adres e-mail** wpisz adres E-mail Britta Simon jako **brittasimon\@contoso.com**.
   
    b. W polu tekstowym **First Name** (Imię) wpisz **imię** użytkownika jako **Britta**.
   
    c. W polu tekstowym **Last Name** (Nazwisko) wpisz **nazwisko** użytkownika jako **Simon**.

4. Kliknij pozycję **Dodaj użytkownika**.
  
    >[!NOTE]
    >Właściciel konta usługi Azure AD otrzyma wiadomość e-mail i użyje linku, aby potwierdzić swoje konto, zanim stanie się ono aktywne. Aby aprowizować konta użytkowników usługi Azure AD, możesz użyć dowolnych innych interfejsów API lub narzędzi do tworzenia kont użytkowników udostępnianych przez usługę Citrix ShareFile.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Citrix ShareFile w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Citrix ShareFile, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

