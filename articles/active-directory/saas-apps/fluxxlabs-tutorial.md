---
title: 'Samouczek: integracja Azure Active Directory z usługą FluxX Labs | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i FluxX Labs.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d8fac770-bb57-4e1f-b50b-9ffeae239d07
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: e624520a9d1f39bc8115ac72e9df0398065928f1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67102389"
---
# <a name="tutorial-azure-active-directory-integration-with-fluxx-labs"></a>Samouczek: integracja Azure Active Directory z usługą FluxX Labs

W tym samouczku dowiesz się, jak zintegrować FluxX Labs z usługą Azure Active Directory (Azure AD).
Integracja FluxX Labs z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do FluxX Labs.
* Możesz umożliwić użytkownikom automatyczne logowanie do FluxX Labs (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą FluxX Labs, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Subskrypcja z włączonym logowaniem jednokrotnym w usłudze FluxX Labs

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa FluxX Labs obsługuje zainicjowane przez **dostawcy tożsamości** Logowanie jednokrotne

## <a name="adding-fluxx-labs-from-the-gallery"></a>Dodawanie FluxX Labs z galerii

Aby skonfigurować integrację FluxX Labs z usługą Azure AD, musisz dodać laboratoria FluxX z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać FluxX Labs z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **FluxX Labs**, wybierz pozycję **FluxX Labs** z panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

     ![FluxX Labs na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą FluxX Labs na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze FluxX Labs.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi FluxX Labs, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania](#configure-fluxx-labs-single-sign-on)** jednokrotnego w usłudze FluxX Labs — w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego FluxX Labs](#create-fluxx-labs-test-user)** , aby dysponować odpowiednikiem Britta Simon w laboratoriach FluxX, które są połączone z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą usługi FluxX Labs, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **FluxX Labs** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** wykonaj następujące kroki:

    ![Informacje logowania jednokrotnego do domeny i adresów URL w usłudze FluxX Labs](common/idp-intiated.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: 

    | Środowisko | Wzorzec adresu URL|
    |-------------|------------|
    | Produkcja | `https://<subdomain>.fluxx.io` |
    | Przed produkcją | `https://<subdomain>.preprod.fluxxlabs.com`|

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: 

    | Środowisko | Wzorzec adresu URL|
    |-------------|------------|
    | Produkcja | `https://<subdomain>.fluxx.io/auth/saml/callback` |
    | Przed produkcją | `https://<subdomain>.preprod.fluxxlabs.com/auth/saml/callback`|

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Skontaktuj się z [zespołem pomocy technicznej FluxX Labs](mailto:travis@fluxxlabs.com) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie FluxX Labs** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-fluxx-labs-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w usłudze FluxX Labs

1. W innym oknie przeglądarki sieci Web Zaloguj się do witryny firmy FluxX Labs jako administrator.

2. Wybierz pozycję **administrator** poniżej sekcji **Ustawienia** .

    ![Konfiguracja FluxX Labs](./media/fluxxlabs-tutorial/config1.png)

3. W panelu administratora wybierz opcję**integracje** dodatków **plug-in** > , a następnie wybierz pozycję **SAML SSO — (wyłączone).**

    ![Konfiguracja FluxX Labs](./media/fluxxlabs-tutorial/config2.png)

4. W sekcji atrybut wykonaj następujące czynności:

    ![Konfiguracja FluxX Labs](./media/fluxxlabs-tutorial/config3.png)

    a. Zaznacz pole wyboru **SAML SSO** .

    b. W polu tekstowym **Ścieżka żądania** wpisz **/AUTH/SAML**.

    c. W polu tekstowym **ścieżka wywołania zwrotnego** wpisz **/AUTH/SAML/callback**.

    d. W polu tekstowym **adres URL usługi konsumenckej potwierdzenia (adres URL logowania** jednokrotnego) wprowadź wartość **adresu URL odpowiedzi** wprowadzoną w Azure Portal.

    e. W polu tekstowym **odbiorcy (identyfikator jednostki SP)** wprowadź wartość **identyfikatora** , która została wprowadzona w Azure Portal.

    f. W polu tekstowym **adres URL logowania JEDNOkrotnego dla dostawcy tożsamości** wklej wartość **adresu URL** , która została skopiowana z Azure Portal.

    g. Otwórz certyfikat zakodowany w formacie Base-64 w Notatniku, skopiuj zawartość tego pliku do schowka, a następnie wklej go do pola tekstowego **certyfikat dostawcy tożsamości** .

    h. W polu tekstowym **Format identyfikatora nazwy** wprowadź wartość `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.

    i. Kliknij przycisk **Zapisz**.

    > [!NOTE]
    > Po zapisaniu zawartości pole będzie wyświetlane jako puste dla zabezpieczeń, ale wartość została zapisana w konfiguracji.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz brittasimon@yourcompanydomain.extension. Na przykład: BrittaSimon@contoso.com

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

Ta sekcja umożliwia włączenie usługi Britta Simon w celu korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usług FluxX Labs.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **FluxX Labs**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **FluxX Labs**.

    ![Link FluxX Labs na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-fluxx-labs-test-user"></a>Tworzenie użytkownika testowego FluxX Labs

Aby umożliwić użytkownikom usługi Azure AD logowanie się do FluxX Labs, muszą one być obsługiwane w usłudze FluxX Labs. W przypadku Fluxxych laboratoriów Inicjowanie obsługi administracyjnej jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy FluxX Labs jako administrator.

2. Kliknij poniżej wyświetlaną **ikonę**.

    ![Konfiguracja FluxX Labs](./media/fluxxlabs-tutorial/config6.png)

3. Na pulpicie nawigacyjnym kliknij poniżej ikonę wyświetlaną, aby otworzyć kartę **nowe osoby** .

    ![Konfiguracja FluxX Labs](./media/fluxxlabs-tutorial/config4.png)

4. W sekcji **nowe osoby** wykonaj następujące czynności:

    ![Konfiguracja FluxX Labs](./media/fluxxlabs-tutorial/config5.png)

    a. FluxX Labs używają poczty e-mail jako unikatowego identyfikatora logowania jednokrotnego (SSO). Wypełnij pole **UID logowania jednokrotnego** , podając adres e-mail użytkownika, który jest zgodny z adresem e-mail, który jest używany jako logowanie za pomocą logowania jednokrotnego.

    b. Kliknij przycisk **Zapisz**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka FluxX Labs w panelu dostępu należy automatycznie zalogować się do laboratoriów FluxX, dla których skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

