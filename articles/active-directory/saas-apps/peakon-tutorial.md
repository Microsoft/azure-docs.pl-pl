---
title: 'Samouczek: integracja Azure Active Directory z usługą Peakon | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Peakon.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: jeedes
ms.openlocfilehash: a84d4d71e5190c455441d1e627381be86ef5e129
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97608531"
---
# <a name="tutorial-azure-active-directory-integration-with-peakon"></a>Samouczek: integracja Azure Active Directory z usługą Peakon

W tym samouczku dowiesz się, jak zintegrować usługę Peakon z usługą Azure Active Directory (Azure AD).
Integracja Peakon z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do usługi Peakon.
* Możesz pozwolić użytkownikom na automatyczne logowanie do Peakon (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą Peakon, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja z włączonym logowaniem jednokrotnym w Peakon

> [!NOTE]
> Ta integracja jest również dostępna do użycia w środowisku chmury dla instytucji rządowych USA usługi Azure AD. Tę aplikację można znaleźć w galerii aplikacji w chmurze dla instytucji rządowych USA usługi Azure AD i skonfigurować ją w taki sam sposób, jak w przypadku chmury publicznej.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Peakon obsługuje usługę **SP** i **dostawcy tożsamości** zainicjowano Logowanie jednokrotne

## <a name="adding-peakon-from-the-gallery"></a>Dodawanie Peakon z galerii

Aby skonfigurować integrację programu Peakon z usługą Azure AD, musisz dodać Peakon z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Peakon z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Peakon**, wybierz pozycję **Peakon** from panel wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

     ![Peakon na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą Peakon na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Peakon.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi Peakon, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie](#configure-peakon-single-sign-on)** jednokrotne w usłudze Peakon, aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego Peakon](#create-peakon-test-user)** , aby uzyskać odpowiednik Britta Simon w Peakon, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą Peakon, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Peakon** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wykonaj następujące czynności:

    ![Peakon domenę i adresy URL Logowanie jednokrotne](common/idp-intiated.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://app.peakon.com/saml/<companyid>/metadata`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://app.peakon.com/saml/<companyid>/assert`

5. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    ![Zrzut ekranu pokazujący, że wybrano "Ustaw dodatkowy U R ls" z wyróżnionym polem tekstowym ""](common/metadata-upload-additional-signon.png)

    W polu tekstowym **adres URL logowania** wpisz adres URL:  `https://app.peakon.com/login`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL odpowiedzi, który został wyjaśniony w dalszej części samouczka. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (nieprzetworzony)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificateraw.png)

7. W sekcji **Konfigurowanie Peakon** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-peakon-single-sign-on"></a>Konfigurowanie pojedynczego Sign-On Peakon

1. W innym oknie przeglądarki sieci Web Zaloguj się do Peakon jako administrator.

2. Na pasku menu po lewej stronie strony kliknij pozycję **Konfiguracja**, a następnie przejdź do obszaru **integracje**.

    ![Konfiguracja](./media/peakon-tutorial/tutorial_peakon_config.png)

3. Na stronie **integracji** kliknij pozycję **Logowanie jednokrotne**.

    ![Pojedyncze](./media/peakon-tutorial/tutorial_peakon_single.png)

4. W sekcji **Logowanie jednokrotne** kliknij pozycję **Włącz**.

    ![Włącz](./media/peakon-tutorial/tutorial_peakon_enable.png)

5. Na stronie Logowanie jednokrotne **dla pracowników korzystających** z języka SAML wykonaj następujące czynności:

    ![Element SAML](./media/peakon-tutorial/tutorial_peakon_saml.png)

    a. W polu tekstowym **adres URL logowania jednokrotnego** , wklej wartość **adresu URL logowania**, która została skopiowana z Azure Portal.

    b. W polu tekstowym **adres URL logowania jednokrotnego** wklej wartość **adresu URL wylogowania**, który został skopiowany z Azure Portal.

    c. Kliknij pozycję **Wybierz plik** , aby przekazać certyfikat pobrany z Azure Portal do pola certyfikat.

    d. Kliknij **ikonę** , aby skopiować **Identyfikator jednostki** i wkleić w polu tekstowym **identyfikatora** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

    e. Kliknij **ikonę** , aby skopiować **adres URL odpowiedzi (ACS)** i wkleić w polu tekstowym **adres URL odpowiedzi** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

    f. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon@yourcompanydomain.extension**  
    Na przykład BrittaSimon@contoso.com

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji Britta Simon do korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Peakon.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **Peakon**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **Peakon**.

    ![Link Peakon na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-peakon-test-user"></a>Utwórz użytkownika testowego Peakon

Aby umożliwić użytkownikom usługi Azure AD logowanie się w usłudze Peakon, muszą one być obsługiwane w Peakon.  
W przypadku Peakon, Inicjowanie obsługi administracyjnej jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do firmowej witryny Peakon jako administrator.

2. Na pasku menu po lewej stronie strony kliknij pozycję **Konfiguracja**, a następnie przejdź do obszaru **pracownicy**.

    ![Pracownik](./media/peakon-tutorial/tutorial_peakon_employee.png)

3. W prawym górnym rogu strony kliknij pozycję **Dodaj pracownika**.

    ![Dodaj pracownika](./media/peakon-tutorial/tutorial_peakon_addemployee.png)

3. Na stronie **nowy pracownik** wykonaj następujące czynności:

    ![Nowy pracownik](./media/peakon-tutorial/tutorial_peakon_create.png)

    1. W polu tekstowym **Nazwa** wpisz imię i nazwisko jako **Britta** i nazwisko jako **Simon**.

    1. W polu tekstowym **adres e-mail** wpisz adres e-mail, na przykład **Brittasimon \@ contoso.com**.

    1. Kliknij pozycję **Utwórz pracownika**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Peakon w panelu dostępu należy automatycznie zalogować się do Peakon, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)