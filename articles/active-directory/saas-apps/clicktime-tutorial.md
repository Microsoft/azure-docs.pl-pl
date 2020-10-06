---
title: 'Samouczek: integracja Azure Active Directory z usługą ClickTime | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją ClickTime.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/21/2019
ms.author: jeedes
ms.openlocfilehash: 98b5c3bd1f41bd3b6a4100fdaebd1d44fb45bf9f
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91760227"
---
# <a name="tutorial-azure-active-directory-integration-with-clicktime"></a>Samouczek: integracja Azure Active Directory z usługą ClickTime

Z tego samouczka dowiesz się, jak zintegrować aplikację ClickTime z usługą Azure Active Directory (Azure AD).
Integracja aplikacji ClickTime z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować w usłudze Azure AD, kto ma dostęp do aplikacji ClickTime.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do aplikacji ClickTime (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z aplikacją ClickTime, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji ClickTime z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja ClickTime obsługuje logowanie jednokrotne inicjowane przez **dostawcę tożsamości**

## <a name="adding-clicktime-from-the-gallery"></a>Dodawanie aplikacji ClickTime z galerii

Aby skonfigurować integrację aplikacji ClickTime z usługą Azure AD, musisz dodać aplikację ClickTime z galerii do swojej listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację ClickTime z galerii, wykonaj następujące kroki:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **ClickTime**, wybierz pozycję **ClickTime** z panelu wyników i kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Aplikacja ClickTime na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z aplikacją ClickTime, korzystając z danych użytkownika testowego **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji ClickTime.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z aplikacją ClickTime, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w aplikacji ClickTime](#configure-clicktime-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego aplikacji ClickTime](#create-clicktime-test-user)** — aby mieć w aplikacji ClickTime odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w aplikacji ClickTime, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **ClickTime** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** wykonaj następujące kroki:

    ![Domena i adresy URL aplikacji ClickTime — informacje o logowaniu jednokrotnym](common/idp-intiated.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL: `https://app.clicktime.com/sp/`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: 
    
    ```http
    https://app.clicktime.com/Login/
    https://app.clicktime.com/App/Login/Consume.aspx
    ```

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie aplikacji ClickTime** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-clicktime-single-sign-on"></a>Konfigurowanie logowania jednokrotnego aplikacji ClickTime

1. W innym oknie przeglądarki internetowej zaloguj się do swojej firmowej witryny aplikacji ClickTime jako administrator.

1. Na pasku narzędzi u góry kliknij pozycję **Preferences** (Preferencje), a następnie kliknij pozycję **Security Settings** (Ustawienia zabezpieczeń).

1. W sekcji **Single Sign-On Preferences** (Preferencje logowania jednokrotnego) wykonaj następujące kroki:
   
    ![Ustawienia zabezpieczeń](./media/clicktime-tutorial/tic777280.png "Ustawienia zabezpieczeń")
   
    a.  Wybierz ustawienie **Allow** sign-in using Single Sign-On (SSO) with **Azure AD** (Zezwalaj na logowanie przy użyciu logowania jednokrotnego za pomocą usługi Azure AD).
   
    b. W polu tekstowym **Identity Provider Endpoint** (Punkt końcowy dostawcy tożsamości) wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.
   
    c.  Otwórz w programie **Notatnik****certyfikat zakodowany w formacie Base-64** pobrany z witryny Azure Portal, skopiuj jego zawartość, a następnie wklej go w polu tekstowym **Certyfikat X.509**.
   
    d.  Kliknij pozycję **Zapisz**.

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

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz dla użytkownika Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji ClickTime.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję **Wszystkie aplikacje**, a następnie wybierz pozycję **ClickTime**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **ClickTime**.

    ![Link aplikacji ClickTime na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-clicktime-test-user"></a>Tworzenie użytkownika testowego aplikacji ClickTime

Aby umożliwić użytkownikom usługi Azure AD logowanie się do aplikacji ClickTime, należy ich aprowizować w aplikacji ClickTime.  
W przypadku aplikacji ClickTime aprowizowanie jest zadaniem ręcznym.

> [!NOTE]
> Aby aprowizować konta użytkowników usługi Azure AD, można użyć dowolnych innych narzędzi tworzenia konta użytkownika aplikacji ClickTime lub interfejsów API udostępnianych przez to rozwiązanie.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do swojej dzierżawy aplikacji **ClickTime**.

1. Na pasku narzędzi u góry kliknij pozycję **Company** (Firma), a następnie kliknij pozycję **People** (Osoby).
   
    ![Zrzut ekranu przedstawia dzierżawcę ClickTime z wybranym przez firmę i użytkownikami.](./media/clicktime-tutorial/tic777282.png "People")

1. Kliknij przycisk **Add Person** (Dodaj osobę).
   
    ![Dodaj osobę](./media/clicktime-tutorial/tic777283.png "Dodaj osobę")

1. W sekcji New Person (Nowa osoba) wykonaj następujące kroki:
   
    ![Zrzut ekranu przedstawia sekcję Dodaj osobę, w której można dodać informacje w tym kroku.](./media/clicktime-tutorial/tic777284.png "People")
   
    a.  W polu tekstowym **full name** (imię i nazwisko) wpisz imię i nazwisko użytkownika, np. **Britta Simon**. 
  
    b.  W polu tekstowym **adres e-mail** wpisz wiadomość e-mail użytkownika, np. **brittasimon \@ contoso.com**.
       
    > [!NOTE]
    > Jeśli chcesz, możesz ustawić dodatkowe właściwości obiektu nowej osoby.
   
    c.  Kliknij pozycję **Zapisz**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka ClickTime w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji ClickTime, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

