---
title: 'Samouczek: integracja Azure Active Directory z oprogramowaniem Cezanne kadr | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Cezanne HR Software.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.openlocfilehash: b5739ea49bb9f5823538e9a41660a572a3cede04
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91760732"
---
# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>Samouczek: integracja Azure Active Directory z oprogramowaniem Cezanne kadr

Z tego samouczka dowiesz się, jak zintegrować aplikację Cezanne HR Software z usługą Azure Active Directory (Azure AD).
Zintegrowanie aplikacji Cezanne HR Software z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD możesz kontrolować, kto ma dostęp do aplikacji Cezanne HR Software.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do aplikacji Cezanne HR Software (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z aplikacją Cezanne HR Software, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji Cezanne HR Software z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Cezanne HR Software obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług**

## <a name="adding-cezanne-hr-software-from-the-gallery"></a>Dodawanie aplikacji Cezanne HR Software z galerii

Aby skonfigurować integrację aplikacji Cezanne HR Software z usługą Azure AD, należy dodać aplikację Cezanne HR Software z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację Cezanne HR Software z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Cezanne HR Software**, wybierz pozycję **Cezanne HR Software** z panelu wyników, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

    ![Aplikacja Cezanne HR Software na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z aplikacją Cezanne HR Software, korzystając z danych testowego użytkownika **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji Cezanne HR Software.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z aplikacją Cezanne HR Software, należy ukończyć poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w aplikacji Cezanne HR Software](#configure-cezanne-hr-software-single-sign-on)**— aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego aplikacji Cezanne HR Software](#create-cezanne-hr-software-test-user)** — aby mieć w aplikacji Cezanne HR Software odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować integrację usługi Azure AD z aplikacją Cezanne HR Software, wykonaj następujące czynności:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **Cezanne HR Software** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje na temat domeny i adresów URL logowania jednokrotnego aplikacji Cezanne HR Software](common/sp-identifier-reply.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://w3.cezanneondemand.com/CezanneOnDemand/-/<tenantidentifier>`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL: `https://w3.cezanneondemand.com/CezanneOnDemand/`

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://w3.cezanneondemand.com:443/cezanneondemand/-/<tenantidentifier>/Saml/samlp`
    
    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj je, używając faktycznego adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta aplikacji Cezanne HR Software](https://cezannehr.com/services/support/) w celu uzyskania tych wartości.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie aplikacji Cezanne HR Software** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-cezanne-hr-software-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w aplikacji Cezanne HR Software

1. W innym oknie przeglądarki internetowej zaloguj się do dzierżawy aplikacji Cezanne HR Software jako administrator.

2. W okienku nawigacji po lewej stronie kliknij pozycję **System Setup** (Konfiguracja systemu). Przejdź do pozycji **Security Settings**. (Ustawienia zabezpieczeń). Następnie przejdź do pozycji **Single Sign-On Configuration** (Konfiguracja logowania jednokrotnego).

    ![Zrzut ekranu przedstawia dzierżawę oprogramowania Cezanne H R z ustawieniami zabezpieczeń i wybraną konfigurację Single Sign-On.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

3. W panelu **Allow users to log in using the following Single Sign-On (SSO) Service** (Zezwalaj użytkownikom na logowanie przy użyciu następującej usługi logowania jednokrotnego) zaznacz pole **SAML 2.0** i wybierz opcję **Advanced Configuration** (Konfiguracja zaawansowana).

    ![Zrzut ekranu pokazuje okienko Zezwalaj użytkownikom z zaznaczoną konfiguracją SAML 2,0 i zaawansowaną.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

4. Kliknij przycisk **Add New** (Dodaj nowe).

    ![Zrzut ekranu przedstawia przycisk Dodaj nowy.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

5. Wykonaj poniższe czynności w sekcji **SAML 2.0 IDENTITY PROVIDERS** (DOSTAWCY TOŻSAMOŚCI SAML 2.0).

    ![Zrzut ekranu przedstawia okienko, w którym można wprowadzić wartości opisane w tym kroku.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)

    a. Wprowadź nazwę dostawcy tożsamości w polu **Display Name** (Nazwa wyświetlana).

    b. W polu tekstowym **Entity Identifier** (Identyfikator jednostki) wklej wartość pola **Identyfikator usługi Azure AD** skopiowaną z witryny Azure Portal.

    c. Zmień wartość pola **SAML Binding** (Powiązanie SAML) na „POST”.

    d. W polu tekstowym **Security Token Service Endpoint** (Punkt końcowy usługi tokenu zabezpieczającego) wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.

    e. W polu tekstowym User ID Attribute Name (Nazwa atrybutu identyfikatora użytkownika) wprowadź `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    f. Kliknij ikonę **Upload** (Przekaż), aby przekazać certyfikat pobrany z witryny Azure Portal.

    przykład Kliknij przycisk **OK**.

6. Kliknij przycisk **Save** (Zapisz).

    ![Zrzut ekranu przedstawia przycisk Zapisz dla konfiguracji logowania jednokrotnego.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon \@ yourcompanydomain. Extension**  
    Na przykład BrittaSimon@contoso.com

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz użytkownikowi Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji Cezanne HR Software.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję **Wszystkie aplikacje**, a następnie wybierz pozycję **Cezanne HR Software**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Cezanne HR Software**.

    ![Link aplikacji Cezanne HR Software na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-cezanne-hr-software-test-user"></a>Tworzenie użytkownika testowego aplikacji Cezanne HR Software

Aby umożliwić użytkownikom usługi Azure AD logowanie do aplikacji Cezanne HR Software, należy ich aprowizować w aplikacji Cezanne HR Software. Aprowizowanie w aplikacji Cezanne HR Software to zadanie wykonywane ręcznie.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do firmowej witryny aplikacji Cezanne HR Software jako administrator.

2. W okienku nawigacji po lewej stronie kliknij pozycję **System Setup** (Konfiguracja systemu). Przejdź do pozycji **Zarządzaj użytkownikami**. Następnie przejdź do pozycji **Add New User** (Dodaj nowego użytkownika).

    ![Zrzut ekranu przedstawia dzierżawę Cezanne H R z funkcją Zarządzanie użytkownikami i wybraną nową użytkownika.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "Nowy użytkownik")

3. W sekcji **PERSON DETAILS** (SZCZEGÓŁY OSOBY) wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia sekcję Szczegóły osoby, w której można wprowadzić wartości opisane w tym kroku.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "Nowy użytkownik")

    a. Ustaw pozycję **Internal User** (Użytkownik wewnętrzny) na OFF (WYŁ.).

    b. W polu tekstowym **imię i nazwisko** wpisz imię użytkownika, np. **Britta**.  

    c. W polu **tekstowym nazwisko wpisz nazwisko użytkownika** , np. **Simon**.

    d. W polu tekstowym **E-mail** wpisz adres e-mail użytkownika, np Brittasimon@contoso.com .

4. W sekcji **Account Information** (Informacje o koncie) wykonaj następujące kroki:

    ![Zrzut ekranu przedstawia informacje o koncie, w którym można wprowadzić wartości opisane w tym kroku.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "Nowy użytkownik")

    a. W polu tekstowym **Username** (Nazwa użytkownika) wpisz adres e-mail użytkownika, na przykład Brittasimon@contoso.com.

    b. W polu tekstowym **Password** (Hasło) wpisz hasło użytkownika.

    c. Wybierz pozycję **HR Professional** (Specjalista ds. kadr) w polu **Security Role** (Rola zabezpieczeń).

    d. Kliknij przycisk **OK**.

5. Przejdź do karty **Single Sign-On** (Logowanie jednokrotne) i wybierz pozycję **Add New** (Dodaj nowy) w obszarze **SAML 2.0 Identifiers** (Identyfikatory języka SAML 2.0).

    ![Zrzut ekranu przedstawia kartę pojedyncze Sign-On, w której można wybrać opcję Dodaj nowy.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "Użytkownik")

6. Wybierz dostawcę tożsamości w polu **Identity Provider** (Dostawca tożsamości) i w polu tekstowym **User Identifier** wprowadź adres e-mail konta użytkownika Britta Simon.

    ![Zrzut ekranu przedstawia identyfikatory SAML 2,0, w których można wybrać dostawcę tożsamości i identyfikator użytkownika.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "Użytkownik")

7. Kliknij przycisk **Save** (Zapisz).

    ![Zrzut ekranu przedstawia przycisk Zapisz dla ustawień użytkownika.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "Użytkownik")

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Cezanne HR Software w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Cezanne HR Software, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
