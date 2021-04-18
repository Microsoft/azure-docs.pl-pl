---
title: 'Samouczek: Azure Active Directory integracji z Tidemark | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie pojedyncze między Azure Active Directory i Tidemark.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 8cbccc3fb4cc513d7c86253ae07fbced4626a15e
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599659"
---
# <a name="tutorial-azure-active-directory-integration-with-tidemark"></a>Samouczek: Azure Active Directory integracji z Tidemark

Z tego samouczka dowiesz się, jak zintegrować narzędzie Tidemark z Azure Active Directory (Azure AD).
Integracja aplikacji Tidemark z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD możesz kontrolować, kto ma dostęp do aplikacji Tidemark.
* Możesz umożliwić swoim użytkownikom automatyczne logowanie do aplikacji Tidemark (logowanie pojedyncze) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji usługi Azure AD z usługą Tidemark potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji Tidemark z obsługą logowania pojedynczego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Znacznik Tidemark obsługuje logowanie **jednokrotne inicjowane** przez sp

## <a name="adding-tidemark-from-the-gallery"></a>Dodawanie aplikacji Tidemark z galerii

Aby skonfigurować integrację aplikacji Tidemark z usługą Azure AD, musisz dodać aplikację Tidemark z galerii do swojej listy zarządzanych aplikacji SaaS.

**Aby dodać znacznik Tidemark z galerii, wykonaj następujące kroki:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Tidemark,** wybierz pozycję **Tidemark** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

     ![Znacznik idemark na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujemy logowanie pojedynczej usługi Azure AD w aplikacji Tidemark, bazując na danych użytkownika testowego **Britta Simon.**
Aby logowanie pojedyncze działało, należy utworzyć relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji Tidemark.

Aby skonfigurować i przetestować logowanie pojedynczej usługi Azure AD w aplikacji Tidemark, należy wykonać czynności z poniższych bloków konstrukcyjnych:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania pojedynczego w aplikacji Tidemark](#configure-tidemark-single-sign-on)** — aby skonfigurować ustawienia logowania Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego aplikacji Tidemark](#create-tidemark-test-user)** — aby mieć w aplikacji Tidemark odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednorazowe usługi Azure AD w aplikacji Tidemark, wykonaj następujące kroki:

1. Na stronie [Azure Portal](https://portal.azure.com/) **aplikacji Tidemark** wybierz **pozycję Logowanie pojedyncze.**

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje o domenie i adresach URL logowania pojedynczego aplikacji Tidemark](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: 

    - `https://<subdomain>.tidemark.com/login`
    - `https://<subdomain>.tidemark.net/login`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: 


    - `https://<subdomain>.tidemark.com/saml`
    - `https://<subdomain>.tidemark.net/saml`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z zespołem pomocy technicznej klienta aplikacji Tidemark, aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W **sekcji Konfigurowanie znacznika Tidemark** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-tidemark-single-sign-on"></a>Konfigurowanie pojedynczego znacznika Tidemark Sign-On

Aby skonfigurować logowanie pojedyncze po stronie aplikacji **Tidemark,** musisz wysłać pobrany certyfikat **(Base64)** i odpowiednie adresy URL skopiowane z aplikacji Azure Portal do zespołu pomocy technicznej aplikacji Tidemark. Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz **pozycję Nowy** użytkownik w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **polu Nazwa użytkownika** wpisz brittasimon@yourcompanydomain.extension . Na przykład BrittaSimon@contoso.com

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz dla użytkownika Britta Simon możliwość korzystania z logowania pojedynczego platformy Azure, udzielając dostępu do aplikacji Tidemark.

1. W Azure Portal wybierz pozycję Aplikacje dla **przedsiębiorstw,** wybierz pozycję **Wszystkie aplikacje,** a następnie wybierz pozycję **Tidemark.**

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Tidemark.**

    ![Link do znacznika Tidemark na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym Wybieranie roli wybierz z  listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk Wybierz w dolnej części ekranu. 

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-tidemark-test-user"></a>Tworzenie użytkownika testowego aplikacji Tidemark

W tej sekcji utworzysz użytkownika Britta Simon w aplikacji Tidemark. We współpracy z zespołem pomocy technicznej aplikacji Tidemark dodaj użytkowników na platformie Tidemark. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Tidemark w aplikacji Panel dostępu powinno na celu to automatyczne zalogowanie się do znacznika Tidemark, dla którego ustawiono logowanie jednokrotne. Aby uzyskać więcej informacji na temat Panel dostępu, [zobacz Wprowadzenie do Panel dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)
