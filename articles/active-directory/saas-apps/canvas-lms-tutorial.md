---
title: 'Samouczek: integracja Azure Active Directory ze kanwą | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Canvas.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: jeedes
ms.openlocfilehash: 6c75103f74ee648c46b805df9495bc668ef4f5bd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91761570"
---
# <a name="tutorial-azure-active-directory-integration-with-canvas"></a>Samouczek: integracja Azure Active Directory ze kanwą

Z tego samouczka dowiesz się, jak zintegrować aplikację Canvas z usługą Azure Active Directory (Azure AD).
Zintegrowanie aplikacji Canvas z usługą Azure AD oferuje następujące korzyści:

* Możesz kontrolować w usłudze Azure AD, kto ma dostęp do aplikacji Canvas.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do aplikacji Canvas (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji usługi Azure AD z aplikacją Canvas potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji Canvas z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Canvas obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług**

## <a name="adding-canvas-from-the-gallery"></a>Dodawanie aplikacji Canvas z galerii

Aby skonfigurować integrację aplikacji Canvas z usługą Azure AD, musisz dodać aplikację Canvas z galerii do swojej listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację Canvas z galerii, wykonaj następujące kroki:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Canvas**, wybierz pozycję **Canvas** z panelu wyników i kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Aplikacja Canvas na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z aplikacją Canvas, korzystając z danych testowego użytkownika **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji Canvas.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z aplikacją Canvas, należy utworzyć poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w aplikacji Canvas](#configure-canvas-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego aplikacji Canvas](#create-canvas-test-user)** — aby mieć w aplikacji Canvas odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w aplikacji Canvas, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **Canvas** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Domena i adresy URL aplikacji Canvas — informacje dotyczące logowania jednokrotnego](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<tenant-name>.instructure.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<tenant-name>.instructure.com/saml2`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. W celu uzyskania tych wartości skontaktuj się z [zespołem pomocy technicznej klienta Canvas](https://community.canvaslms.com/community/help). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. W sekcji **Certyfikat podpisywania SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Certyfikat podpisywania SAML**.

    ![Edytowanie certyfikatu podpisywania SAML](common/edit-certificate.png)

6. W sekcji **Certyfikat podpisywania SAML** skopiuj wartość **ODCISK PALCA** i zapisz go na komputerze.

    ![Kopiowanie wartości Odcisk palca](common/copy-thumbprint.png)

7. W sekcji **Skonfiguruj aplikację Canvas** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-canvas-single-sign-on"></a>Konfigurowanie logowania jednokrotnego aplikacji Canvas

1. W innym oknie przeglądarki internetowej zaloguj się do firmowej witryny aplikacji Canvas jako administrator.

2. Przejdź do pozycji **Kursy \> Konta zarządzane \> Microsoft**.

    ![Kanwa](./media/canvas-lms-tutorial/ic775990.png "Kanwa")

3. W okienku nawigacji po lewej stronie wybierz pozycję **Uwierzytelnianie**, a następnie kliknij pozycję **Dodaj nową konfigurację SAML**.

    ![Authentication](./media/canvas-lms-tutorial/ic775991.png "Authentication")

4. Na stronie Bieżąca integracja wykonaj następujące kroki:

    ![Bieżąca integracja](./media/canvas-lms-tutorial/ic775992.png "Bieżąca integracja")

    a. W polu tekstowym **Identyfikator jednostki dostawcy tożsamości** wklej wartość **identyfikatora usługi Azure AD** skopiowaną z witryny Azure Portal.

    b. W polu tekstowym **Adres URL logowania** wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.

    c. W polu tekstowym **Adres URL wylogowywania** wklej wartość **adresu URL wylogowywania** skopiowaną z witryny Azure Portal.

    d. W polu tekstowym **Link do zmiany hasła** wklej wartość **linku do zmiany hasła** skopiowaną z witryny Azure Portal.

    e. W polu tekstowym **Odcisk palca certyfikatu** wklej wartość **odcisku palca** certyfikatu skopiowaną z witryny Azure Portal.

    f. Z listy **Atrybut logowania** wybierz pozycję **NameID**.

    przykład Z listy **Format identyfikatora** wybierz pozycję **emailAddress**.

    h. Kliknij pozycję **Zapisz ustawienia uwierzytelniania**.

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

W tej sekcji włączysz użytkownikowi Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji Canvas.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję **Wszystkie aplikacje**, a następnie wybierz pozycję **Canvas**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Canvas**.

    ![Link do aplikacji Canvas na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-canvas-test-user"></a>Tworzenie użytkownika testowego aplikacji Canvas

Aby umożliwić użytkownikom usługi Azure AD logowanie się do aplikacji Canvas, muszą być oni aprowizowani w tej aplikacji. W przypadku aplikacji Canvas aprowizowanie użytkowników wykonuje się ręcznie.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do swojej dzierżawy aplikacji **Canvas**.

2. Przejdź do pozycji **Kursy \> Konta zarządzane \> Microsoft**.

   ![Kanwa](./media/canvas-lms-tutorial/ic775990.png "Kanwa")

3. Kliknij pozycję **Użytkownicy**.

   ![Zrzut ekranu przedstawia menu kanwy z wybranymi użytkownikami.](./media/canvas-lms-tutorial/ic775995.png "Użytkownicy")

4. Kliknij pozycję **Dodaj nowego użytkownika**.

   ![Zrzut ekranu przedstawia przycisk Dodaj nowego użytkownika.](./media/canvas-lms-tutorial/ic775996.png "Użytkownicy")

5. W oknie dialogowym Dodawanie nowego użytkownika wykonaj następujące kroki:

   ![Dodaj użytkownika](./media/canvas-lms-tutorial/ic775997.png "Dodaj użytkownika")

   a. W polu tekstowym **Imię i nazwisko** wprowadź nazwę użytkownika, na przykład **BrittaSimon**.

   b. W polu tekstowym **adres e-mail** wprowadź adres e-mail użytkownika, np. **brittasimon \@ contoso.com**.

   c. W polu tekstowym **Login (logowanie** ) wprowadź adres E-mail użytkownika usługi Azure AD, taki jak **brittasimon \@ contoso.com**.

   d. Wybierz pozycję **Poinformuj użytkownika pocztą e-mail o utworzeniu tego konta**.

   e. Kliknij pozycję **Dodaj użytkownika**.

> [!NOTE]
> Możesz użyć innych narzędzi do tworzenia kont użytkowników kanwy lub interfejsów API udostępnionych przez kanwę do udostępniania kont użytkowników usługi Azure AD.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Canvas w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Canvas, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

