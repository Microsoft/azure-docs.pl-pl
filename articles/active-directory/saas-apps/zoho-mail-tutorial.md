---
title: 'Samouczek: integracja Azure Active Directory z usługą Zoho | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Zoho.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jeedes
ms.openlocfilehash: 00b9df3ad5e4d2104051e5489138785b4ecde8e0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88546149"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho"></a>Samouczek: integracja Azure Active Directory z usługą Zoho

Z tego samouczka dowiesz się, jak zintegrować aplikację Zoho z usługą Azure Active Directory (Azure AD).
Integracja aplikacji Zoho z usługą Azure AD oferuje następujące korzyści:

* W usłudze Azure AD możesz kontrolować, kto ma dostęp do aplikacji Zoho.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do aplikacji Zoho (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji usługi Azure AD z aplikacją Zoho potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji Zoho z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Zoho obsługuje logowanie jednokrotne inicjowane przez **SP**

## <a name="adding-zoho-from-the-gallery"></a>Dodawanie aplikacji Zoho z galerii

Aby skonfigurować integrację aplikacji Zoho z usługą Azure AD, musisz dodać aplikację Zoho z galerii do swojej listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację Zoho z galerii, wykonaj następujące kroki:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Zoho**, wybierz pozycję **Zoho** z panelu wyników i kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Aplikacja Zoho na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z aplikacją Zoho, korzystając z danych testowego użytkownika **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji Zoho.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z aplikacją Zoho, należy utworzyć poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w aplikacji Zoho](#configure-zoho-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego aplikacji Zoho](#create-zoho-test-user)** — aby mieć w aplikacji Zoho odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w aplikacji Zoho, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **Zoho** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje o domenie i adresach URL logowania jednokrotnego aplikacji Zoho](common/sp-signonurl.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<company name>.zohomail.com`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta Zoho](https://www.zoho.com/mail/contact.html), aby uzyskać tę wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Skonfiguruj aplikację Zoho** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-zoho-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w aplikacji Zoho

1. W innym oknie przeglądarki internetowej zaloguj się do firmowej witryny usługi Zoho Mail jako administrator.

2. Przejdź do obszaru **Control panel** (Panel sterowania).
   
    ![Panel sterowania](./media/zoho-mail-tutorial/ic789607.png "Panel sterowania")

3. Kliknij kartę **SAML Authentication** (Uwierzytelnianie SAML).
   
    ![Uwierzytelnianie SAML](./media/zoho-mail-tutorial/ic789608.png "Uwierzytelnianie SAML")

4. W sekcji **SAML Authentication Details** (Szczegóły uwierzytelniania SAML) wykonaj następujące kroki:
   
    ![Szczegóły uwierzytelniania SAML](./media/zoho-mail-tutorial/ic789609.png "Szczegóły uwierzytelniania SAML")
   
    a. W polu tekstowym **Login URL** (Adres URL logowania) wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.
   
    b. W polu tekstowym **Logout URL** (Adres URL wylogowywania) wklej wartość **adresu URL wylogowywania** skopiowaną z witryny Azure Portal.
   
    c. W polu tekstowym **Change Password URL** (Adres URL zmiany hasła) wklej wartość **adresu URL zmiany hasła** skopiowaną z witryny Azure Portal.
       
    d. Otwórz w Notatniku certyfikat zakodowany w formacie Base-64 pobrany z witryny Azure Portal, skopiuj zawartość do Schowka, a następnie wklej ją w polu tekstowym **PublicKey** (Klucz publiczny).
   
    e. Dla ustawienia **Algorithm** (Algorytm) wybierz pozycję **RSA**.
   
    f. Kliknij przycisk **OK**.

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

W tej sekcji włączysz użytkownikowi Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji Zoho.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję **Wszystkie aplikacje**, a następnie wybierz pozycję **Zoho**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wpisz i wybierz ciąg **Zoho**.

    ![Link aplikacji Zoho na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-zoho-test-user"></a>Tworzenie użytkownika testowego aplikacji Zoho

Aby umożliwić użytkownikom usługi Azure AD logowanie się do aplikacji Zoho Mail, należy ich aprowizować w aplikacji Zoho Mail. W przypadku aplikacji Zoho Mail aprowizację wykonuje się ręcznie.

> [!NOTE]
> Do udostępniania kont użytkowników usługi Azure AD można używać innych narzędzi do tworzenia kont użytkowników poczty Zoho lub interfejsów API dostarczonych przez pocztę Zoho.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Aby aprowizować konto użytkownika, wykonaj następujące czynności:

1. Zaloguj się do firmowej witryny usługi **Zoho Mail** jako administrator.

1. Przejdź do pozycji **Control Panel \> Mail & Docs** (Panel sterowania > Poczta i dokumenty).

1. Przejdź do pozycji **User Details \> Add User** (Szczegóły użytkownika > Dodaj użytkownika).
   
    ![Dodaj użytkownika](./media/zoho-mail-tutorial/ic789611.png "Dodaj użytkownika")

1. W oknie dialogowym **Add users** (Dodawanie użytkowników) wykonaj następujące kroki:
   
    ![Dodaj użytkownika](./media/zoho-mail-tutorial/ic789612.png "Dodaj użytkownika")
   
    a. W polu tekstowym **First Name** (Imię) wpisz imię użytkownika, takie jak **Britta**.

    b. W polu tekstowym **Last Name** (Nazwisko) wpisz nazwisko użytkownika, takie jak **Simon**.

    c. W polu tekstowym **Identyfikator wiadomości e-mail** wpisz identyfikator poczty e-mail użytkownika, np **. \@ brittasimon contoso.com**.

    d. W polu tekstowym **Password** (Hasło) wprowadź hasło użytkownika.
   
    e. Kliknij przycisk **OK**.  
      
    > [!NOTE]
    > Właściciel konta usługi Azure Active Directory otrzyma wiadomość e-mail z linkiem w celu potwierdzenia konta, zanim stanie się ono aktywne.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Zoho w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Zoho, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

