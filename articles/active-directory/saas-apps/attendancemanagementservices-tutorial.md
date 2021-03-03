---
title: 'Samouczek: integracja Azure Active Directory z usługami zarządzania frekwencją | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między usługami Azure Active Directory i frekwencji.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.openlocfilehash: 1f404d3613f9de8daadc4bb2ceb39282cf3b619e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101688998"
---
# <a name="tutorial-azure-active-directory-integration-with-attendance-management-services"></a>Samouczek: integracja Azure Active Directory z usługami zarządzania frekwencją

W tym samouczku dowiesz się, jak zintegrować usługi zarządzania frekwencją z usługą Azure Active Directory (Azure AD).
Integrowanie usług zarządzania frekwencją z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do usług zarządzania obecnością.
* Możesz umożliwić użytkownikom automatyczne logowanie do usług zarządzania frekwencją (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację z usługą Azure AD za pomocą usług zarządzania frekwencją, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Subskrypcja obsługująca Logowanie jednokrotne usług zarządzania obecnością

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługi zarządzania frekwencją obsługują Logowanie jednokrotne w usłudze **SP**

## <a name="adding-attendance-management-services-from-the-gallery"></a>Dodawanie usług zarządzania frekwencją z galerii

Aby skonfigurować integrację usług zarządzania frekwencją z usługą Azure AD, musisz dodać usługi zarządzania frekwencją z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać usługi zarządzania frekwencją z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **usługi zarządzania obecnościami**, wybierz opcję **usługi zarządzania obecnością** z panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![Usługi zarządzania obecnościami na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji należy skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą usług zarządzania frekwencją na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze zarządzania frekwencją.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą usług zarządzania frekwencją, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj Logowanie jednokrotne usług zarządzania frekwencją](#configure-attendance-management-services-single-sign-on)** — aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego usług zarządzania frekwencją](#create-attendance-management-services-test-user)** , aby uzyskać odpowiednik usługi Britta Simon w usłudze zarządzania frekwencjami, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne w usłudze Azure AD za pomocą usług zarządzania frekwencją, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **usług zarządzania obecności** wybierz opcję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje dotyczące logowania jednokrotnego w ramach domen i adresów URL usług zarządzania obecnością](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://id.obc.jp/<tenant information >/`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://id.obc.jp/<tenant information >/`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z [zespołem obsługi klienta usług zarządzania frekwencją](https://www.obcnet.jp/) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie usług zarządzania frekwencją** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-attendance-management-services-single-sign-on"></a>Skonfiguruj pojedyncze Sign-On usług zarządzania frekwencją

1. W innym oknie przeglądarki Zaloguj się do witryny firmy usług zarządzania frekwencją jako administrator.

1. Kliknij pozycję **uwierzytelnianie SAML** w **sekcji Zarządzanie zabezpieczeniami**.

    ![Zrzut ekranu przedstawia uwierzytelnianie SAML wybrane na stronie używającej znaków innych niż łacińskie.](./media/attendancemanagementservices-tutorial/user1.png)

1. Wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia okno, w którym można wykonać zadania opisane w tym kroku.](./media/attendancemanagementservices-tutorial/user2.png)

    a. Wybierz opcję **Użyj uwierzytelniania SAML**.

    b. W polu tekstowym **Identyfikator** wklej wartość **identyfikatora usługi Azure AD** , która została skopiowana z Azure Portal.

    c. W polu tekstowym **adres URL punktu końcowego uwierzytelniania** wklej wartość **adresu URL logowania** , która została skopiowana z Azure Portal.

    d. Kliknij pozycję **Wybierz plik** , aby przekazać certyfikat pobrany z usługi Azure AD.

    e. Wybierz pozycję **Wyłącz uwierzytelnianie hasła**.

    f. Kliknij pozycję **rejestracja**

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz `brittasimon@yourcompanydomain.extension` . Na przykład BrittaSimon@contoso.com

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

Ta sekcja umożliwia włączenie usługi Britta Simon w celu korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usług zarządzania obecnością.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **usługi zarządzania frekwencją**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **usługi zarządzania obecnością**.

    ![Link usług zarządzania obecności na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-attendance-management-services-test-user"></a>Tworzenie użytkownika testowego usług zarządzania obecnością

Aby umożliwić użytkownikom usługi Azure AD logowanie się do usług zarządzania obecnością, muszą one być obsługiwane w usługach zarządzania frekwencją. W przypadku usług zarządzania obecnością Inicjowanie obsługi administracyjnej jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do swojej witryny firmowej usług zarządzania frekwencją jako administrator.

1. Kliknij pozycję **Zarządzanie użytkownikami** w **sekcji Zarządzanie zabezpieczeniami**.

    ![Zrzut ekranu przedstawia zarządzanie użytkownikami wybrane na stronie używającej znaków innych niż łacińskie.](./media/attendancemanagementservices-tutorial/user5.png)

1. Kliknij kolejno pozycje **nowe reguły logowanie**.

    ![Zrzut ekranu przedstawia wybranie opcji Plus.](./media/attendancemanagementservices-tutorial/user3.png)

1. W sekcji **Informacje o OBCiD** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia okno, w którym można wykonać opisane zadania.](./media/attendancemanagementservices-tutorial/user4.png)

    a. W polu tekstowym **OBCiD** wpisz adres e-mail użytkownika, np `BrittaSimon@contoso.com` .

    b. W polu tekstowym **Password** (Hasło) wpisz hasło użytkownika.

    c. Kliknij pozycję **rejestracja**

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka usługi zarządzania obecności w panelu dostępu należy automatycznie zalogować się do usług zarządzania obecnością, dla których skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)