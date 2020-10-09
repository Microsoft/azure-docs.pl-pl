---
title: 'Samouczek: integracja Azure Active Directory z usługą MobileIron | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją MobileIron.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.openlocfilehash: 45ad613fd250f905dd1cd5e14b5c8aeb42896a51
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88554657"
---
# <a name="tutorial-azure-active-directory-integration-with-mobileiron"></a>Samouczek: integracja Azure Active Directory z usługą MobileIron

Z tego samouczka dowiesz się, jak zintegrować aplikację MobileIron z usługą Azure Active Directory (Azure AD).
Integrowanie aplikacji MobileIron z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD możesz kontrolować, kto ma dostęp do aplikacji MobileIron.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do aplikacji MobileIron (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji usługi Azure AD z aplikacją MobileIron są potrzebne następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji MobileIron z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja MobileIron obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług i dostawcę tożsamości**

## <a name="adding-mobileiron-from-the-gallery"></a>Dodawanie aplikacji MobileIron z galerii

Aby skonfigurować integrację aplikacji MobileIron z usługą Azure AD, musisz dodać aplikację MobileIron z galerii do swojej listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację MobileIron z galerii, wykonaj następujące kroki:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **MobileIron**, wybierz pozycję **MobileIron** z panelu wyników i kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Aplikacja MobileIron na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z aplikacją MobileIron, korzystając z danych testowego użytkownika o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji MobileIron.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z aplikacją MobileIron, należy ukończyć następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w aplikacji MobileIron](#configure-mobileiron-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego aplikacji MobileIron](#create-mobileiron-test-user)** — aby w aplikacji MobileIron utworzyć odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w aplikacji MobileIron, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **MobileIron** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **dostawcę tożsamości**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki:

    ![Domena i adresy URL aplikacji MobileIron — informacje dotyczące logowania jednokrotnego](common/idp-intiated.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://www.mobileiron.com/<key>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<host>.mobileiron.com/saml/SSO/alias/<key>`

    c. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    ![Domena i adresy URL aplikacji MobileIron — informacje dotyczące logowania jednokrotnego](common/metadata-upload-additional-signon.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<host>.mobileiron.com/user/login.html`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Wartości klucza i hosta uzyskasz z portalu administracyjnego aplikacji MobileIron, co zostało wyjaśnione w dalszej części tego samouczka.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

### <a name="configure-mobileiron-single-sign-on"></a>Konfigurowanie logowania jednokrotnego aplikacji MobileIron

1. W innym oknie przeglądarki internetowej zaloguj się do firmowej witryny aplikacji MobileIron jako administrator.

2. Przejdź do pozycji **Administracja**  >  **tożsamość** i wybierz opcję **AAD** w polu **informacje na stronie Konfiguracja dostawcy tożsamości w chmurze** .

    ![Konfigurowanie przycisku Administrator logowania jednokrotnego](./media/mobileiron-tutorial/tutorial_mobileiron_admin.png)

3. Skopiuj wartości **klucza** i **hosta** oraz wklej je, aby ukończyć adresy URL w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

    ![Konfigurowanie przycisku Administrator logowania jednokrotnego](./media/mobileiron-tutorial/key.png)

4. W pozycji **Eksportowanie pliku metadanych z usługi AAD i importowanie go do pola chmury aplikacji MobileIron** kliknij pozycję **Wybierz plik**, aby przekazać metadane pobrane z witryny Azure Portal. Po zakończeniu przekazywania kliknij przycisk **Gotowe**.

    ![Konfigurowanie przycisku metadanych administratora logowania jednokrotnego](./media/mobileiron-tutorial/tutorial_mobileiron_adminmetadata.png)

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

W tej sekcji włączysz dla użytkownika Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji MobileIron.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję **Wszystkie aplikacje**, a następnie wybierz pozycję **MobileIron**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wpisz i wybierz ciąg **MobileIron**.

    ![Link aplikacji MobileIron na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-mobileiron-test-user"></a>Tworzenie użytkownika testowego aplikacji MobileIron

Aby umożliwić użytkownikom usługi Azure AD logowanie się do aplikacji MobileIron, należy aprowizować ich w aplikacji MobileIron.  
W przypadku aplikacji MobileIron aprowizowanie jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do firmowej witryny aplikacji MobileIron jako administrator.

1. Przejdź do pozycji **Użytkownicy** i kliknij pozycję **Dodaj** > **Pojedynczy użytkownik**.

    ![Konfigurowanie przycisku użytkownika logowania jednokrotnego](./media/mobileiron-tutorial/tutorial_mobileiron_user.png)

1. Na stronie okna dialogowego **„Pojedynczy użytkownik”** wykonaj następujące kroki:

    ![Konfigurowanie przycisku dodawania użytkownika logowania jednokrotnego](./media/mobileiron-tutorial/tutorial_mobileiron_useradd.png)

    a. W polu tekstowym **adres e-mail** Wprowadź wiadomość e-mail użytkownika brittasimon@contoso.com .

    b. W polu tekstowym **imię i nazwisko** Wprowadź imię użytkownika, np. Britta.

    c. W polu tekstowym **nazwisko** Wprowadź nazwisko użytkownika, np. Simon.

    d. Kliknij pozycję **Gotowe**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka MobileIron w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji MobileIron, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

