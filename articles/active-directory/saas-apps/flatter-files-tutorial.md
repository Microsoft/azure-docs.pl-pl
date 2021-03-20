---
title: 'Samouczek: integracja Azure Active Directory z plikami Flatter | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Flatter Files.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.openlocfilehash: fae21c98c7340614a96b6e85aa79bdea7889a807
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92453247"
---
# <a name="tutorial-azure-active-directory-integration-with-flatter-files"></a>Samouczek: integracja Azure Active Directory z plikami Flatter

Z tego samouczka dowiesz się, jak zintegrować aplikację Flatter Files z usługą Azure Active Directory (Azure AD).
Integracja aplikacji Flatter Files z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD możesz kontrolować, kto ma dostęp do aplikacji Flatter Files.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do aplikacji Flatter Files (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji usługi Azure AD z aplikacją Flatter Files potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji Flatter Files z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Flatter Files obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług**

## <a name="adding-flatter-files-from-the-gallery"></a>Dodawanie aplikacji Flatter Files z galerii

Aby skonfigurować integrację aplikacji Flatter Files w usłudze Azure AD, należy dodać aplikację Flatter Files z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację Flatter Files z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Flatter Files**, wybierz pozycję **Flatter Files** z panelu wyników i kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Aplikacja Flatter Files na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z aplikacją Flatter Files, korzystając z danych użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji Flatter Files.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD w aplikacji Flatter Files, należy wykonać kroki opisane w poniższych blokach konstrukcyjnych:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w aplikacji Flatter Files](#configure-flatter-files-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego aplikacji Flatter Files](#create-flatter-files-test-user)** — aby mieć w aplikacji Flatter Files odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w aplikacji Flatter Files, wykonaj następujące czynności:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **Flatter Files** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** użytkownik nie musi wykonywać żadnych kroków, ponieważ aplikacja jest już wstępnie zintegrowana z platformą Azure.

    ![Informacje o domenie i adresach URL logowania jednokrotnego aplikacji Flatter Files](common/preintegrated.png)

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie aplikacji Flatter Files** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-flatter-files-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w aplikacji Flatter Files

1. Zaloguj się do aplikacji Flatter File jako administrator.

2. Kliknij pozycję **DASHBOARD** (Pulpit nawigacyjny). 
   
    ![Zrzut ekranu pokazujący "pulpit NAWIGACYJNy" wybrany w aplikacji "pliki Flatter".](./media/flatter-files-tutorial/tutorial_flatter_files_05.png)  

3. Kliknij pozycję **Settings** (Ustawienia), a następnie wykonaj następujące czynności na karcie **Company** (Firma): 
   
    ![Zrzut ekranu przedstawiający kartę "Firma" z opcją "Użyj S A M L 2,0 do uwierzytelnienia" i wybranym przyciskiem "Konfiguruj S A M L".](./media/flatter-files-tutorial/tutorial_flatter_files_06.png)  
    
    a. Wybierz pozycję **Use SAML 2.0 For Authentication** (Użyj protokołu SAML 2.0 na potrzeby uwierzytelniania).
    
    b. Kliknij pozycję **Skonfiguruj język SAML**.

4. W oknie dialogowym **SAML Configuration** (Konfiguracja SAML) wykonaj następujące kroki: 
   
    ![Konfigurowanie logowania jednokrotnego](./media/flatter-files-tutorial/tutorial_flatter_files_08.png)  
   
    a. W polu tekstowym **Domain** (Domena) wpisz nazwę zarejestrowanej domeny.
   
   > [!NOTE]
   > Jeśli nie masz jeszcze zarejestrowanej domeny, skontaktuj się z pomocą techniczną, aby uzyskać pomoc dotyczącą Twoich plików Flatter [support@flatterfiles.com](mailto:support@flatterfiles.com) . 
    
    b. W polu tekstowym **Identity Provider URL** (Adres URL dostawcy tożsamości) wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.
   
    c.  Otwórz certyfikat zakodowany w formacie Base-64 w Notatniku, skopiuj zawartość tego pliku do schowka, a następnie wklej go do pola tekstowego **certyfikat dostawcy tożsamości** .

    d. Kliknij przycisk **Update** (Aktualizuj).

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

W tej sekcji włączysz dla użytkownika Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji Flatter Files.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, pozycję **Wszystkie aplikacje**, a następnie pozycję **Flatter Files**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Flatter Files**.

    ![Link do aplikacji Flatter Files na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-flatter-files-test-user"></a>Tworzenie użytkownika testowego aplikacji Flatter Files

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w aplikacji Flatter Files.

**Aby utworzyć użytkownika o nazwie Britta Simon w aplikacji Flatter Files, wykonaj następujące kroki:**

1. Zaloguj się w witrynie firmowej **Flatter Files** jako administrator.

2. W okienku nawigacji po lewej stronie kliknij pozycję **Settings** (Ustawienia), a następnie kartę **Users** (Użytkownicy).
   
    ![Zrzut ekranu przedstawiający stronę "Ustawienia" z wybraną kartą "Użytkownicy".](./media/flatter-files-tutorial/tutorial_flatter_files_09.png)

3. Kliknij pozycję **Dodaj użytkownika**. 

4. W oknie dialogowym **Add User** (Dodawanie użytkownika) wykonaj następujące kroki:
   
    ![Tworzenie użytkownika aplikacji Flatter Files](./media/flatter-files-tutorial/tutorial_flatter_files_10.png)

    a. W polu tekstowym **imię i nazwisko** wpisz **Britta**.
   
    b. W polu **tekstowym nazwisko** wpisz **Simon**. 
   
    c. W polu tekstowym **Email Address** (Adres e-mail) wpisz adres e-mail użytkownika Britta w witrynie Azure Portal.
   
    d. Kliknij przycisk **Prześlij**.   


### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Flatter Files w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Flatter Files, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)