---
title: 'Samouczek: integracja Azure Active Directory z usługą potencjalnie szkodliwe oprogramowanie | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i potencjalnie szkodliwe oprogramowanie.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: b147037a58e4af59c0c3ccd56b6f6b241d775308
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88553341"
---
# <a name="tutorial-azure-active-directory-integration-with-riskware"></a>Samouczek: integracja Azure Active Directory z usługą potencjalnie szkodliwe oprogramowanie

W tym samouczku dowiesz się, jak zintegrować usługę potencjalnie szkodliwe oprogramowanie z usługą Azure Active Directory (Azure AD).
Integracja potencjalnie szkodliwe oprogramowanie z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do usługi potencjalnie szkodliwe oprogramowanie.
* Możesz pozwolić użytkownikom na automatyczne logowanie do potencjalnie szkodliwe oprogramowanie (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą potencjalnie szkodliwe oprogramowanie, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Subskrypcja z włączonym logowaniem jednokrotnym w potencjalnie szkodliwe oprogramowanie

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Potencjalnie szkodliwe oprogramowanie obsługuje logowanie jednokrotne w usłudze **SP**

## <a name="adding-riskware-from-the-gallery"></a>Dodawanie potencjalnie szkodliwe oprogramowanie z galerii

Aby skonfigurować integrację programu potencjalnie szkodliwe oprogramowanie z usługą Azure AD, musisz dodać potencjalnie szkodliwe oprogramowanie z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać potencjalnie szkodliwe oprogramowanie z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **potencjalnie szkodliwe oprogramowanie**, wybierz pozycję **potencjalnie szkodliwe oprogramowanie** from panel wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![Potencjalnie szkodliwe oprogramowanie na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą potencjalnie szkodliwe oprogramowanie na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w potencjalnie szkodliwe oprogramowanie.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi potencjalnie szkodliwe oprogramowanie, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie](#configure-riskware-single-sign-on)** jednokrotne w usłudze potencjalnie szkodliwe oprogramowanie, aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego potencjalnie szkodliwe oprogramowanie](#create-riskware-test-user)** , aby uzyskać odpowiednik Britta Simon w potencjalnie szkodliwe oprogramowanie, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą potencjalnie szkodliwe oprogramowanie, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **potencjalnie szkodliwe oprogramowanie** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Potencjalnie szkodliwe oprogramowanie domenę i adresy URL Logowanie jednokrotne](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: 
    
    | Środowisko| Wzorzec adresu URL|
    |--|--|
    | Testowanie akceptacyjne przez użytkowników|  `https://riskcloud.net/uat?ccode=<COMPANYCODE>` |
    | PROD| `https://riskcloud.net/prod?ccode=<COMPANYCODE>` |
    | PREZENTOWAN| `https://riskcloud.net/demo?ccode=<COMPANYCODE>` |
    |||

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL: 
    
    | Środowisko| Wzorzec adresu URL|
    |--|--|
    | Testowanie akceptacyjne przez użytkowników| `https://riskcloud.net/uat` |
    | PROD| `https://riskcloud.net/prod` |
    | PREZENTOWAN| `https://riskcloud.net/demo` |
    |||

    > [!NOTE]
    > Wartość adresu URL logowania nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z [zespołem obsługi klienta potencjalnie szkodliwe oprogramowanie](mailto:support@pansoftware.com.au) , aby uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W sekcji **Konfigurowanie potencjalnie szkodliwe oprogramowanie** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-riskware-single-sign-on"></a>Konfigurowanie pojedynczego Sign-On potencjalnie szkodliwe oprogramowanie

1. W innym oknie przeglądarki sieci Web Zaloguj się do firmowej witryny potencjalnie szkodliwe oprogramowanie jako administrator.

1. W prawym górnym rogu kliknij pozycję **konserwacja** , aby otworzyć stronę konserwacja.

    ![Obsługa konfiguracji potencjalnie szkodliwe oprogramowanie](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. Na stronie obsługa kliknij pozycję **uwierzytelnianie**.

    ![Potencjalnie szkodliwe oprogramowanie konfiguracja AUTHEN](./media/riskware-tutorial/tutorial_riskware_authen.png)

1. Na stronie **konfiguracja uwierzytelniania** wykonaj następujące czynności:

    ![Potencjalnie szkodliwe oprogramowanie konfiguracja authenconfig](./media/riskware-tutorial/tutorial_riskware_config.png)

    a. Wybierz **Typ** jako **SAML** na potrzeby uwierzytelniania.

    b. W polu tekstowym **kod** wpisz swój kod, taki jak AZURE_UAT.

    c. W polu tekstowym **Opis** wpisz swój opis, taki jak konfiguracja platformy Azure na potrzeby rejestracji jednokrotnej.

    d. W polu tekstowym **na stronie logowania** jednokrotnego wklej wartość **adresu URL logowania** , która została skopiowana z Azure Portal.

    e. W polu tekstowym **Wyrejestruj stronę** , wklej wartość **adresu URL wylogowania** , która została skopiowana z Azure Portal.

    f. W **polu tekstowym pole formularza post** wpisz nazwę pola znajdującą się w odpowiedzi post, która zawiera element SAML like SAMLResponse

    przykład W polu tekstowym **nazwa tagu tożsamości XML** , atrybut typu, który zawiera unikatowy identyfikator w odpowiedzi SAML, np. NameID.

    h. Otwórz pobrany **plik XML metadanych** z Azure Portal w Notatniku, Skopiuj certyfikat z pliku metadanych i wklej go do pola tekstowego **certyfikatu**

    i. W polu tekstowym **adres URL klienta** wklej wartość **adresu URL odpowiedzi**, którą otrzymujesz od zespołu pomocy technicznej.

    j. W polu tekstowym **wystawca** wklej wartość **identyfikatora**, którą otrzymujesz od zespołu pomocy technicznej.

    > [!Note]
    > Skontaktuj się z [zespołem obsługi klienta potencjalnie szkodliwe oprogramowanie](mailto:support@pansoftware.com.au) , aby uzyskać te wartości

    k. Zaznacz pole wyboru **Użyj wpisu** .

    l. Zaznacz pole wyboru **Użyj żądania SAML** .

    m. Kliknij przycisk **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz `brittasimon@yourcompanydomain.extension`  
    Na przykład BrittaSimon@contoso.com

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji Britta Simon do korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi potencjalnie szkodliwe oprogramowanie.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **potencjalnie szkodliwe oprogramowanie**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **potencjalnie szkodliwe oprogramowanie**.

    ![Link potencjalnie szkodliwe oprogramowanie na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-riskware-test-user"></a>Utwórz użytkownika testowego potencjalnie szkodliwe oprogramowanie

Aby umożliwić użytkownikom usługi Azure AD logowanie się w usłudze potencjalnie szkodliwe oprogramowanie, muszą one być obsługiwane w usłudze potencjalnie szkodliwe oprogramowanie. W potencjalnie szkodliwe oprogramowanie, Inicjowanie obsługi jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do potencjalnie szkodliwe oprogramowanie jako administrator zabezpieczeń.

1. W prawym górnym rogu kliknij pozycję **konserwacja** , aby otworzyć stronę konserwacja. 

    ![Potencjalnie szkodliwe oprogramowanie konfiguracja](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. Na stronie obsługa kliknij pozycję **osoby**.

    ![Potencjalnie szkodliwe oprogramowanie osoby konfiguracji](./media/riskware-tutorial/tutorial_riskware_people.png)

1. Wybierz kartę **szczegóły** i wykonaj następujące czynności:

    ![Szczegóły konfiguracji potencjalnie szkodliwe oprogramowanie](./media/riskware-tutorial/tutorial_riskware_details.png)

    a. Wybierz **Typ osoby** podobnej do pracownika.

    b. W polu tekstowym **imię i nazwisko** , wprowadź imię użytkownika, np. **Britta**.

    c. W polu tekstowym **nazwisko** Wprowadź nazwisko użytkownika, np. **Simon**.

1. Na karcie **zabezpieczenia** wykonaj następujące czynności:

    ![Potencjalnie szkodliwe oprogramowanie zabezpieczeń konfiguracji](./media/riskware-tutorial/tutorial_riskware_security.png)

    a. W obszarze **uwierzytelnianie** wybierz tryb **uwierzytelniania** , który został skonfigurowany tak, jak konfiguracja platformy Azure na potrzeby logowania jednokrotnego.

    b. W obszarze **szczegóły logowania** w polu tekstowym **Identyfikator użytkownika** wprowadź adres e-mail użytkownika, np `brittasimon@contoso.com` .

    c. W polu tekstowym **hasło** wprowadź hasło użytkownika.

1. Na karcie **organizacja** wykonaj następujące czynności:

    ![Organizacja konfiguracji potencjalnie szkodliwe oprogramowanie](./media/riskware-tutorial/tutorial_riskware_org.png)

    a. Wybierz opcję jako organizację **Level1** .

    b. W obszarze **obszar roboczy osoby głównej** , w polu tekstowym **Lokalizacja** wpisz swoją lokalizację.

    c. W obszarze **Pracownik** wybierz pozycję **Stan pracownika** , np..

    d. Kliknij przycisk **Zapisz**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka potencjalnie szkodliwe oprogramowanie w panelu dostępu należy automatycznie zalogować się do potencjalnie szkodliwe oprogramowanie, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
