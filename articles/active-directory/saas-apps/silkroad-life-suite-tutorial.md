---
title: 'Samouczek: integracja Azure Active Directory z pakietem SilkRoad Life | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i SilkRoad Life Suite.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: 89d086ce136885e203e300f04bdbf3ade0affeb3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92516056"
---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>Samouczek: integracja Azure Active Directory z pakietem Life SilkRoad

W tym samouczku dowiesz się, jak zintegrować pakiet SilkRoad Life Suite z usługą Azure Active Directory (Azure AD).
Integracja pakietu SilkRoad Life z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do pakietu SilkRoad Life Suite.
* Możesz umożliwić użytkownikom automatyczne logowanie do SilkRoad Life Suite (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z pakietem SilkRoad Life Suite, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Subskrypcja z włączonym logowaniem jednokrotnym w pakiecie SilkRoad

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* SilkRoad Life Suite obsługuje logowanie jednokrotne w usłudze **SP**

## <a name="adding-silkroad-life-suite-from-the-gallery"></a>Dodawanie pakietu SilkRoad Life z galerii

Aby skonfigurować integrację pakietu SilkRoad Life z usługą Azure AD, musisz dodać pakiet SilkRoad Life z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać pakiet SilkRoad Life z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **SilkRoad Life Suite**, wybierz pozycję **SilkRoad Life Suite** z panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![SilkRoad Life Suite na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji należy skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą SilkRoad Life Suite na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w pakiecie SilkRoad Life.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą pakietu SilkRoad Life Suite, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie](#configure-silkroad-life-suite-single-sign-on)** jednokrotne w usłudze SilkRoad Life, aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego SilkRoad Life Suite](#create-silkroad-life-suite-test-user)** , aby dysponować odpowiednikiem Britta Simon w pakiecie SilkRoad Life, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą pakietu SilkRoad Life Suite, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja z aplikacją **SilkRoad Life** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML**, jeśli masz **plik metadanych dostawcy usługi**, wykonaj następujące kroki:

    > [!NOTE]
    > Zapoznaj się z **plikiem metadanych dostawcy usług** opisanym w dalszej części tego samouczka.

    a. Kliknij pozycję **Przekaż plik metadanych**.

    ![Zrzut ekranu przedstawia podstawową konfigurację języka SAML przy użyciu linku Przekaż plik metadanych.](common/upload-metadata.png)

    b. Kliknij **logo folderu**, aby wybrać plik metadanych, a następnie kliknij pozycję **Przekaż**.

    ![Zrzut ekranu przedstawia okno dialogowe, w którym można wybrać i przekazać plik.](common/browse-upload-metadata.png)

    c. Po pomyślnym przekazaniu pliku metadanych wartości **identyfikatorów** i **adresów URL odpowiedzi** są automatycznie wypełniane w sekcji Podstawowa konfiguracja SAML:

    ![Zrzut ekranu przedstawia podstawową konfigurację języka SAML, w której można wprowadzić identyfikator, odpowiedź U R L i wybrać pozycję Zapisz.](common/sp-identifier-reply.png)

    > [!Note]
    > Jeśli wartości **Identyfikator** i **Adres URL odpowiedzi** nie zostaną automatycznie wypełnione, wpisz te wartości ręcznie zgodnie z wymaganiami.

    d. W polu tekstowym **adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<subdomain>.silkroad-eng.com/Authentication/`

5. Jeśli nie masz **pliku metadanych dostawcy usług**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje logowania jednokrotnego w ramach pakietu SilkRoad Life i adresów URL](common/sp-identifier-reply.png)

    a. W polu tekstowym **adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<subdomain>.silkroad-eng.com/Authentication/`

    b. W polu **Identyfikator** wpisz adres URL, korzystając z następującego wzorca:

    - `https://<subdomain>.silkroad-eng.com/Authentication/SP`
    - `https://<subdomain>.silkroad.com/Authentication/SP`

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: 

    - `https://<subdomain>.silkroad-eng.com/Authentication/`
    - `https://<subdomain>.silkroad.com/Authentication/`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami adresu URL logowania, identyfikatora i adresu URL odpowiedzi. Skontaktuj się z [zespołem pomocy technicznej SilkRoad Life Suite](https://www.silkroad.com/locations/) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

7. W sekcji **Konfigurowanie SilkRoad Life Suite** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-silkroad-life-suite-single-sign-on"></a>Konfigurowanie pojedynczego Sign-On pakietu Life Suite SilkRoad

1. Zaloguj się do witryny firmy SilkRoad jako administrator.

    > [!NOTE]
    > Aby uzyskać dostęp do aplikacji uwierzytelniania SilkRoad Life Suite na potrzeby konfigurowania Federacji z Microsoft Azure AD, skontaktuj się z pomocą techniczną SilkRoad lub przedstawicielem usług SilkRoad Services.

1. Przejdź do pozycji **dostawca usług**, a następnie kliknij pozycję **szczegóły Federacji**.

    ![Zrzut ekranu przedstawia szczegóły Federacji wybrane z dostawcy usług.](./media/silkroad-life-suite-tutorial/tutorial_silkroad_06.png)

1. Kliknij pozycję **Pobierz metadane federacji**, a następnie Zapisz plik metadanych na komputerze. Użyj pobranych metadanych Federacji jako **pliku metadanych dostawcy usług** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

    ![Zrzut ekranu przedstawia łącze pobierania metadanych Federacji.](./media/silkroad-life-suite-tutorial/tutorial_silkroad_07.png)

1. W aplikacji **Silkroad** kliknij pozycję **źródła uwierzytelniania**.

    ![Zrzut ekranu przedstawia wybrane źródła uwierzytelniania.](./media/silkroad-life-suite-tutorial/tutorial_silkroad_08.png) 

1. Kliknij pozycję **Dodaj źródło uwierzytelniania**.

    ![Zrzut ekranu przedstawia link Dodaj źródło uwierzytelniania.](./media/silkroad-life-suite-tutorial/tutorial_silkroad_09.png)

1. W sekcji **Dodaj źródło uwierzytelniania** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia przycisk Dodaj źródło uwierzytelniania z opcją Utwórz dostawcę tożsamości przy użyciu danych plików.](./media/silkroad-life-suite-tutorial/tutorial_silkroad_10.png)
  
    a. W obszarze **Opcja 2 — plik metadanych** kliknij przycisk **Przeglądaj** , aby przekazać pobrany plik metadanych z Azure Portal.
  
    b. Kliknij pozycję **Utwórz dostawcę tożsamości przy użyciu danych pliku**.

1. W sekcji **źródła uwierzytelniania** kliknij pozycję **Edytuj**.

    ![Zrzut ekranu przedstawia źródła uwierzytelniania z wybraną opcją edycji.](./media/silkroad-life-suite-tutorial/tutorial_silkroad_11.png)

1. W oknie dialogowym **Edytowanie źródła uwierzytelniania** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia okno dialogowe Edytowanie źródła uwierzytelniania, w którym można wprowadzić podane wartości.](./media/silkroad-life-suite-tutorial/tutorial_silkroad_12.png)

    a. Jako **włączone** wybierz pozycję **tak**.

    b. W polu tekstowym **EntityId** wklej wartość **identyfikatora usługi Azure AD** , który został skopiowany z Azure Portal.

    c. W polu tekstowym **Opis dostawcy tożsamości** wpisz opis swojej konfiguracji (na przykład: *Logowanie jednokrotne w usłudze Azure AD*).

    d. W polu tekstowym **plik metadanych** Przekaż plik **metadanych** , który został pobrany z Azure Portal.
  
    e. W polu tekstowym **Nazwa dostawcy tożsamości** wpisz nazwę specyficzną dla konfiguracji (na przykład: *Azure Sp*).
  
    f. W polu tekstowym **adres URL usługi Wyloguj** wklej wartość **adresu URL wylogowywania** skopiowanego z Azure Portal.

    przykład W polu tekstowym **adres URL usługi logowania** wklej wartość **adresu URL logowania** skopiowanego z Azure Portal.

    h. Kliknij pozycję **Zapisz**.

1. Wyłącz wszystkie inne źródła uwierzytelniania.

    ![Zrzut ekranu przedstawia źródła uwierzytelniania, w których można wyłączyć inne źródła. ](./media/silkroad-life-suite-tutorial/tutorial_silkroad_13.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz `brittasimon@yourcompanydomain.extension`  
    Na przykład BrittaSimon@contoso.com

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

Ta sekcja umożliwia włączenie usługi Britta Simon w celu korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do pakietu SilkRoad Life Suite.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **SilkRoad Life Suite**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **SilkRoad Life Suite**.

    ![Link SilkRoad Life Suite na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-silkroad-life-suite-test-user"></a>Tworzenie użytkownika testowego SilkRoad Life Suite

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w pakiecie SilkRoad Life. Współpracuj z [zespołem pomocy technicznej SilkRoad Life Suite](https://www.silkroad.com/locations/) , aby dodać użytkowników z platformy Silkroad Suite. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka SilkRoad Life Suite w panelu dostępu należy automatycznie zalogować się do pakietu SilkRoad Life, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)