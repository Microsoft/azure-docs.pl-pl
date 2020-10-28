---
title: 'Samouczek: Azure Active Directory integrację z logowaniem jednokrotnym SAML dla Confluence za pomocą rozwiązania GmbH | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a aplikacją SAML SSO for Confluence by resolution GmbH.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.openlocfilehash: 7c36dd87ac305db62d2d80b74de09f4389722da9
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675507"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-confluence-by-resolution-gmbh"></a>Samouczek: integracja Azure Active Directory z logowaniem jednokrotnym SAML dla Confluence przez rozwiązanie GmbH

Z tego samouczka dowiesz się, jak zintegrować aplikację SAML SSO for Confluence by resolution GmbH z usługą Azure Active Directory (Azure AD).
Zintegrowanie aplikacji SAML SSO for Confluence by resolution GmbH z usługą Azure AD zapewnia następujące korzyści:

* Możliwość kontrolowania dostępu do aplikacji SAML SSO for Confluence by resolution GmbH za pomocą usługi Azure AD.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do aplikacji SAML SSO for Confluence by resolution GmbH (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji usługi Azure AD z aplikacją SAML SSO for Confluence by resolution GmbH potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji SAML SSO for Confluence by resolution GmbH z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja SAML SSO for Confluence by resolution GmbH obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług** i **dostawcę tożsamości**

## <a name="adding-saml-sso-for-confluence-by-resolution-gmbh-from-the-gallery"></a>Dodawanie aplikacji SAML SSO for Confluence by resolution GmbH z galerii

Aby skonfigurować integrację aplikacji SAML SSO for Confluence by resolution GmbH z usługą Azure AD, należy z poziomu galerii dodać tę aplikację do listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację SAML SSO for Confluence by resolution GmbH z galerii, wykonaj następujące kroki:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory** .

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje** .

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **SAML SSO for Confluence by resolution GmbH** , wybierz pozycję **SAML SSO for Confluence by resolution GmbH** z panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

     ![Pozycja SAML SSO for Confluence by resolution GmbH na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z aplikacją SAML SSO for Confluence by resolution GmbH, korzystając z danych testowego użytkownika **Britta Simon** .
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji SAML SSO for Confluence by resolution GmbH.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z aplikacją SAML SSO for Confluence by resolution GmbH, należy wykonać czynności opisane w poniższych blokach konstrukcyjnych:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w aplikacji SAML SSO for Confluence by resolution GmbH](#configure-saml-sso-for-confluence-by-resolution-gmbh-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego aplikacji SAML SSO for Confluence by resolution GmbH](#create-saml-sso-for-confluence-by-resolution-gmbh-test-user)** — aby w aplikacji SAML SSO for Confluence by resolution GmbH istniał odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD z aplikacją SAML SSO for Confluence by resolution GmbH, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **SAML SSO for Confluence by resolution GmbH** wybierz pozycję **Logowanie jednokrotne** .

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed** , aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj** , aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML** .

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Aby skonfigurować aplikację w trybie inicjowania **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wykonaj następujące czynności:

    ![Informacje o domenie i adresach URL aplikacji SAML SSO for Confluence by resolution GmbH na potrzeby logowania jednokrotnego](common/idp-intiated.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://<server-base-url>/plugins/servlet/samlsso`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez dostawcę usług:

    ![Informacje o domenie i adresach URL aplikacji SAML SSO for Confluence by resolution GmbH na potrzeby logowania jednokrotnego](common/metadata-upload-additional-signon.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta aplikacji SAML SSO for Confluence by resolution GmbH](https://www.resolution.de/go/support), aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz** , aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

### <a name="configure-saml-sso-for-confluence-by-resolution-gmbh-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w aplikacji SAML SSO for Confluence by resolution GmbH

1. W innym oknie przeglądarki internetowej zaloguj się do **portalu administracyjnego aplikacji SAML SSO for Confluence by resolution GmbH** jako administrator.

2. Zatrzymaj wskaźnik myszy nad ikoną koła zębatego i kliknij pozycję **Dodatki** .
    
    ![Konfigurowanie logowania jednokrotnego](./media/samlssoconfluence-tutorial/addon1.png)

3. Nastąpi przekierowanie na stronę dostępu administratora. Wprowadź hasło, a następnie kliknij przycisk **Confirm** (Potwierdź).

    ![Konfigurowanie logowania jednokrotnego](./media/samlssoconfluence-tutorial/addon2.png)

4. Na karcie **ATLASSIAN MARKETPLACE** (Platforma handlowa Atlassian) kliknij pozycję **Find new add-ons** (Znajdź nowe dodatki). 

    ![Konfigurowanie logowania jednokrotnego](./media/samlssoconfluence-tutorial/addon.png)

5. Wyszukaj pozycję **SAML Single Sign On (SSO) for Confluence** , a następnie kliknij przycisk **Install** (Zainstaluj), aby zainstalować nową wtyczkę protokołu SAML.

    ![Konfigurowanie logowania jednokrotnego](./media/samlssoconfluence-tutorial/addon7.png)

6. Rozpocznie się instalacja wtyczki. Kliknij przycisk **Zamknij** .

    ![Konfigurowanie logowania jednokrotnego](./media/samlssoconfluence-tutorial/addon8.png)

    ![Konfigurowanie logowania jednokrotnego](./media/samlssoconfluence-tutorial/addon9.png)

7.  Kliknij pozycję **Zarządzaj** .

    ![Konfigurowanie logowania jednokrotnego](./media/samlssoconfluence-tutorial/addon10.png)
    
8. Kliknij pozycję **Configure** (Konfiguruj), aby skonfigurować nową wtyczkę.

    ![Konfigurowanie logowania jednokrotnego](./media/samlssoconfluence-tutorial/addon11.png)

9. Ten nowy dodatek plug-in można także znaleźć na karcie **USERS & SECURITY** (Użytkownicy i zabezpieczenia).

    ![Konfigurowanie logowania jednokrotnego](./media/samlssoconfluence-tutorial/addon3.png)
    
10. Na stronie **SAML SingleSignOn Plugin Configuration** (Konfiguracja wtyczki logowania jednokrotnego protokołu SAML) kliknij przycisk **Add new IdP** (Dodaj nowego dostawcę tożsamości), aby skonfigurować ustawienia dostawcy tożsamości.

    ![Konfigurowanie logowania jednokrotnego](./media/samlssoconfluence-tutorial/addon4.png)

11. Na stronie **Choose your SAML Identity Provider** (Wybieranie dostawcy tożsamości protokołu SAML) wykonaj następujące kroki:

    ![Konfigurowanie logowania jednokrotnego](./media/samlssoconfluence-tutorial/addon5a.png)
 
    a. W polu IdP type (Typ dostawcy tożsamości) wybierz pozycję **Azure AD** .
    
    b. W polu **Name** (Nazwa) wprowadź nazwę dostawcy tożsamości, np. Azure AD.
    
    c. W polu **Description** (Opis) wprowadź opis dostawcy tożsamości, np. Azure AD.
    
    d. Kliknij przycisk **Dalej** .
    
12. Na stronie **Identity provider configuration** (Konfiguracja dostawcy tożsamości) kliknij przycisk **Next** (Dalej).

    ![Konfigurowanie logowania jednokrotnego](./media/samlssoconfluence-tutorial/addon5b.png)

13. Na stronie **Import SAML IdP Metadata** (Importuj metadane dostawcy tożsamości protokołu SAML) wykonaj następujące kroki:

    ![Konfigurowanie logowania jednokrotnego](./media/samlssoconfluence-tutorial/addon5c.png)

    a. Kliknij przycisk **Load File** (Załaduj plik), a następnie wybierz plik XML metadanych pobrany w kroku 5.

    b. Kliknij przycisk **Import** (Importuj).
    
    c. Zaczekaj chwilę na pomyślne zakończenie importowania.
    
    d. Kliknij przycisk **Next** (Dalej).
    
14. Na stronie **User ID attribute and transformation** (Atrybut identyfikatora użytkownika i transformacja) kliknij przycisk **Next** (Dalej).

    ![Konfigurowanie logowania jednokrotnego](./media/samlssoconfluence-tutorial/addon5d.png)
    
15. Na stronie **User creation and update** (Tworzenie i aktualizowanie użytkownika) kliknij pozycję **Save & Next** (Zapisz i przejdź dalej), aby zapisać ustawienia.   
    
    ![Konfigurowanie logowania jednokrotnego](./media/samlssoconfluence-tutorial/addon6a.png)
    
16. Na stronie **Test your settings** (Przetestuj ustawienia) kliknij pozycję **Skip test & configure manually** (Pomiń test i skonfiguruj ręcznie), aby pominąć na razie test użytkownika. Zostanie on wykonany w następnej sekcji i będzie wymagał wprowadzenia pewnych ustawień w witrynie Azure Portal. 
    
    ![Konfigurowanie logowania jednokrotnego](./media/samlssoconfluence-tutorial/addon6b.png)
    
17. W wyświetlonym oknie dialogowym odczytywanie **pominięcia testu...** , kliknij przycisk **OK** .
    
    ![Konfigurowanie logowania jednokrotnego](./media/samlssoconfluence-tutorial/addon6c.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory** , wybierz opcję **Użytkownicy** , a następnie wybierz pozycję **Wszyscy użytkownicy** .

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon** .
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon \@ yourcompanydomain. Extension**  
    Na przykład BrittaSimon@contoso.com

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz** .

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz dla użytkownika Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji SAML SSO for Confluence by resolution GmbH.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw** , wybierz pozycję **Wszystkie aplikacje** , a następnie wybierz pozycję **SAML SSO for Confluence by resolution GmbH** .

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wpisz **SAML SSO for Confluence by resolution GmbH** i wybierz odpowiednią pozycję.

    ![Link do aplikacji SAML SSO for Confluence by resolution GmbH na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy** .

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika** , a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania** .

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

### <a name="create-saml-sso-for-confluence-by-resolution-gmbh-test-user"></a>Tworzenie użytkownika testowego aplikacji SAML SSO for Confluence by resolution GmbH

Aby umożliwić użytkownikom usługi Azure AD logowanie się do aplikacji SAML SSO for Confluence by resolution GmbH, należy aprowizować ich w tym rozwiązaniu.  
W przypadku aplikacji SAML SSO for Confluence by resolution GmbH aprowizację wykonuje się ręcznie.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do firmowej witryny aplikacji SAML SSO for Confluence by resolution GmbH jako administrator.

2. Zatrzymaj wskaźnik myszy nad ikoną koła zębatego i kliknij pozycję **User management** (Zarządzanie użytkownikami).

    ![Dodawanie pracownika](./media/samlssoconfluence-tutorial/user1.png) 

3. W obszarze Użytkownicy kliknij przycisk **Dodaj użytkowników** kartę. Na stronie **"Dodawanie użytkownika"** wykonaj następujące czynności:

    ![Dodawanie pracownika](./media/samlssoconfluence-tutorial/user2.png) 

    a. W polu tekstowym **Username** (Nazwa użytkownika) wpisz adres e-mail użytkownika, np. Britta Simon.

    b. W polu tekstowym **Full Name** (Imię i nazwisko) wpisz imię i nazwisko użytkownika, np. Britta Simon.

    c. W polu tekstowym **Email** (Adres e-mail) wpisz adres e-mail użytkownika, np. Brittasimon@contoso.com.

    d. W polu tekstowym **Password** (Hasło) wpisz hasło użytkownika Britta Simon.

    e. Kliknij pozycję **Confirm Password** (Potwierdź hasło), a następnie ponownie wprowadź hasło.
    
    f. Kliknij przycisk **Add** (Dodaj).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka SAML SSO for Confluence by resolution GmbH w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji SAML SSO for Confluence by resolution GmbH, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)