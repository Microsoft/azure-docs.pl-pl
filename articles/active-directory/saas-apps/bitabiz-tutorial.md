---
title: 'Samouczek: integracja Azure Active Directory z usługą BitaBIZ | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją BitaBIZ.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/06/2019
ms.author: jeedes
ms.openlocfilehash: 729427dac174037c8750af7ab0f6cb17fa4d4c1e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91741487"
---
# <a name="tutorial-azure-active-directory-integration-with-bitabiz"></a>Samouczek: integracja Azure Active Directory z usługą BitaBIZ

Z tego samouczka dowiesz się, jak zintegrować aplikację BitaBIZ z usługą Azure Active Directory (Azure AD).
Zintegrowanie aplikacji BitaBIZ z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować w usłudze Azure AD, kto ma dostęp do aplikacji BitaBIZ.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do aplikacji BitaBIZ (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z aplikacją BitaBIZ, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji BitaBIZ z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja BitaBIZ obsługuje logowanie jednokrotne inicjowane przez **dostawcę usługi oraz dostawcę tożsamości**

## <a name="adding-bitabiz-from-the-gallery"></a>Dodawanie aplikacji BitaBIZ z galerii

Aby skonfigurować integrację aplikacji BitaBIZ z usługą Azure AD, musisz dodać aplikację BitaBIZ z galerii do swojej listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację BitaBIZ z galerii, wykonaj następujące kroki:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **BitaBIZ**, wybierz pozycję **BitaBIZ** z panelu wyników i kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Aplikacja BitaBIZ na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z aplikacją BitaBIZ, korzystając z danych użytkownika testowego **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji BitaBIZ.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z aplikacją BitaBIZ, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w aplikacji BitaBIZ](#configure-bitabiz-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego aplikacji BitaBIZ](#create-bitabiz-test-user)** — aby mieć w aplikacji BitaBIZ odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD z aplikacją BitaBIZ, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **BitaBIZ** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **dostawcę tożsamości**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następującą czynność:

    ![Informacje o domenie i adresach URL aplikacji BitaBIZ na potrzeby logowania jednokrotnego](common/idp-identifier.png)

    W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://www.bitabiz.com/<instanceId>`

    > [!NOTE]
    > Wartość w powyższym adresie URL ma charakter wyłącznie demonstracyjny. Zaktualizuj tę wartość przy użyciu rzeczywistego identyfikatora, co objaśniono w dalszej części tego samouczka.

5. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    ![image (obraz)](common/both-preintegrated-signon.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL: `https://www.bitabiz.com/dashboard`

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

7. W sekcji **Konfigurowanie aplikacji BitaBIZ** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-bitabiz-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w aplikacji BitaBIZ

1. W oddzielnym oknie przeglądarki internetowej zaloguj się do dzierżawy aplikacji BitaBIZ jako administrator.

2. Kliknij pozycję **SETUP ADMIN** (ADMINISTRATOR KONFIGURACJI).

    ![Zrzut ekranu przedstawia część okna przeglądarki z wybraną pozycją administrator instalacji.](./media/bitabiz-tutorial/settings1.png)

3. Kliknij pozycję **Microsoft integrations** (Integracja w rozwiązaniach firmy Microsoft) w sekcji **Add value** (Dodawanie wartości).

    ![Zrzut ekranu przedstawia wybrane wartości Dodaj wartość z integracją firmy Microsoft.](./media/bitabiz-tutorial/settings2.png)

4. Przewiń w dół do sekcji **Microsoft Azure AD (Enable single sign on)** (Usługa Microsoft Azure AD (włącz logowanie jednokrotne)) i wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia sekcję Microsoft Azure D, w której wprowadzane są informacje opisane w tym kroku.](./media/bitabiz-tutorial/settings3.png)

    a. Skopiuj wartość z pola tekstowego **Entity ID (”Identifier” in Azure AD)** (Identyfikator jednostki („Identyfikator” w usłudze Azure AD)) i wklej ją w polu tekstowym **Identyfikator** w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal. 

    b. W polu tekstowym **Azure AD Sign On URL** (Adres URL logowania usługi Azure AD) wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.

    c. W polu tekstowym **Azure AD SAML Entity ID** (Identyfikator jednostki SAML usługi Azure AD) wklej wartość **identyfikatora usługi Azure AD** skopiowaną z witryny Azure Portal.

    d. Otwórz pobrany plik **Certificate(Base64)** w Notatniku, skopiuj jego zawartość do schowka, a następnie wklej ją w polu tekstowym **Azure AD Signing Certificate (Base64 encoded)** (Certyfikat podpisywania usługi Azure AD (zakodowany algorytmem Base64)).

    e. Dodaj swoją firmową domenę poczty e-mail, tj. mycompany.com, w polu tekstowym **Domain name** (Nazwa domeny), aby przypisać logowanie jednokrotne do użytkowników w Twojej firmie z tą domeną poczty e-mail (nie jest to obowiązkowe).

    f. Oznacz jako **SSO enabled** (Włączone logowanie jednokrotne) konto aplikacji BitaBIZ.

    przykład Kliknij przycisk **Save Azure AD configuration** (Zapisz konfigurację usługi Azure AD), aby zapisać i aktywować konfigurację logowania jednokrotnego.

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

W tej sekcji włączysz dla użytkownika Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji BitaBIZ.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję **Wszystkie aplikacje**, a następnie wybierz pozycję **BitaBIZ**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **BitaBIZ**.

    ![Link aplikacji BitaBIZ na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-bitabiz-test-user"></a>Tworzenie użytkownika testowego aplikacji BitaBIZ

Aby umożliwić użytkownikom usługi Azure AD logowanie do aplikacji BitaBIZ, należy aprowizować ich w aplikacji BitaBIZ.  
W przypadku aplikacji BitaBIZ aprowizowanie jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do swojej firmowej witryny aplikacji BitaBIZ jako administrator.

2. Kliknij pozycję **SETUP ADMIN** (ADMINISTRATOR KONFIGURACJI).

    ![Zrzut ekranu przedstawia część okna przeglądarki z wybranym administratorem instalacji.](./media/bitabiz-tutorial/settings1.png)

3. Kliknij pozycję **Add users** (Dodaj użytkowników) w sekcji **Organization** (Organizacja).

    ![Zrzut ekranu przedstawia sekcję organizacja z wybraną pozycją Dodaj użytkowników.](./media/bitabiz-tutorial/user1.png)

4. Kliknij przycisk **Add new employee** (Dodaj nowego pracownika).

    ![Zrzut ekranu pokazuje Dodaj użytkowników z wybranym nowym pracownikiem.](./media/bitabiz-tutorial/user2.png)

5. Na stronie okna dialogowego **Add new employee** (Dodawanie nowego pracownika) wykonaj następujące kroki:

    ![Zrzut ekranu przedstawia stronę, na której wprowadzane są informacje opisane w tym kroku.](./media/bitabiz-tutorial/user3.png)

    a. W polu tekstowym **imię i nazwisko** wpisz imię użytkownika, np. Britta.

    b. W polu **tekstowym nazwisko wpisz nazwisko użytkownika** , np. Simon.

    c. W polu tekstowym **Email** (Adres e-mail) wpisz adres e-mail użytkownika, np. Brittasimon@contoso.com.

    d. Wybierz datę w polu **Date of employment** (Data zatrudnienia).

    e. Istnieją inne nieobowiązkowe atrybuty użytkownika, które można skonfigurować. Zapoznaj się z [dokumentem dotyczącym konfiguracji pracownika](https://help.bitabiz.dk/manage-or-set-up-your-account/on-boarding-employees/new-employee), aby uzyskać więcej informacji.

    f. Kliknij przycisk **Save employee** (Zapisz pracownika).

    > [!NOTE]
    > Właściciel konta usługi Azure Active Directory otrzyma wiadomość e-mail z linkiem umożliwiającym potwierdzenie konta, zanim stanie się ono aktywne.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka BitaBIZ w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji BitaBIZ, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
