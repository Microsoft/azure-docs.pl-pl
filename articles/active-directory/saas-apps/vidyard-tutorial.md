---
title: 'Samouczek: integracja Azure Active Directory z usługą Vidyard | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Vidyard.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 4176c92d48b67b9f9207f22ebd8939b5ec1437ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92636752"
---
# <a name="tutorial-azure-active-directory-integration-with-vidyard"></a>Samouczek: integracja Azure Active Directory z usługą Vidyard

W tym samouczku dowiesz się, jak zintegrować usługę Vidyard z usługą Azure Active Directory (Azure AD).
Integracja Vidyard z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do usługi Vidyard.
* Możesz pozwolić użytkownikom na automatyczne logowanie do Vidyard (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą Vidyard, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Subskrypcja z włączonym logowaniem jednokrotnym w Vidyard

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Vidyard obsługuje usługę **SP** i **dostawcy tożsamości** zainicjowano Logowanie jednokrotne

* Vidyard obsługuje Inicjowanie obsługi użytkowników **just in Time**

## <a name="adding-vidyard-from-the-gallery"></a>Dodawanie Vidyard z galerii

Aby skonfigurować integrację programu Vidyard z usługą Azure AD, musisz dodać Vidyard z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Vidyard z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Vidyard**, wybierz pozycję **Vidyard** from panel wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

     ![Vidyard na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą Vidyard na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Vidyard.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi Vidyard, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie](#configure-vidyard-single-sign-on)** jednokrotne w usłudze Vidyard, aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego Vidyard](#create-vidyard-test-user)** , aby uzyskać odpowiednik Britta Simon w Vidyard, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą Vidyard, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Vidyard** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia podstawową konfigurację języka SAML, w której można wprowadzić identyfikator, odpowiedź U R L i wybrać pozycję Zapisz.](common/idp-intiated.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://secure.vidyard.com/sso/saml/<unique id>/metadata`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://secure.vidyard.com/sso/saml/<unique id>/consume`

5. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    ![Zrzut ekranu przedstawia ustawienie dodatkowego U R LS, gdzie można wprowadzić znak U R L.](common/metadata-upload-additional-signon.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://secure.vidyard.com/sso/saml/<unique id>/login`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Te wartości zostaną zaktualizowane za pomocą rzeczywistego identyfikatora, adresu URL odpowiedzi i adresu URL Sign-On, co zostało wyjaśnione w dalszej części tego samouczka. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

7. W sekcji **Konfigurowanie Vidyard** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-vidyard-single-sign-on"></a>Konfigurowanie pojedynczego Sign-On Vidyard

1. W innym oknie przeglądarki sieci Web Zaloguj się do swojej witryny firmy Vidyard jako administrator.

2. Na pulpicie nawigacyjnym Vidyard wybierz pozycję  >  **zabezpieczenia** grupy

    ![Zrzut ekranu przedstawia zabezpieczenia wybrane z grupy w witrynie Vidyard Software.](./media/vidyard-tutorial/configure1.png)

3. Kliknij pozycję **Nowy profil** kartę.

    ![Zrzut ekranu przedstawia przycisk Nowy profil.](./media/vidyard-tutorial/configure2.png)

4. W sekcji **Konfiguracja SAML** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia sekcję Konfiguracja protokołu SAML, w której można wprowadzić podane wartości.](./media/vidyard-tutorial/configure3.png)

    a. Wprowadź nazwę profilu ogólnego w polu tekstowym **Nazwa profilu** .

    b. Skopiuj wartość **strony logowania jednokrotnego użytkownika** i wklej ją do pola tekstowego **Zaloguj adres URL** w sekcji **Podstawowa konfiguracja SAML** na Azure Portal.

    c. Skopiuj wartość **adres URL ACS** i wklej ją do pola tekstowego **adresu URL odpowiedzi** w sekcji **Podstawowa konfiguracja SAML** na Azure Portal.

    d. Skopiuj wartość **wystawcy/adres URL metadanych** i wklej ją do pola tekstowego **identyfikatora** w sekcji **Podstawowa konfiguracja SAML** na Azure Portal.

    e. Otwórz pobrany plik certyfikatu z Azure Portal w Notatniku, a następnie wklej go do pola tekstowego **certyfikatu X. 509** .

    f. W polu tekstowym **adres URL punktu końcowego SAML** wklej wartość **adresu URL logowania** skopiowanego z Azure Portal.

    przykład Kliknij pozycję **Potwierdź**.

5. Na karcie Logowanie jednokrotne wybierz pozycję **Przypisz** obok istniejącego profilu

    ![Zrzut ekranu przedstawia przycisk Przypisz dla profilu Azure A D s s.](./media/vidyard-tutorial/configure4.png)

    > [!NOTE]
    > Po utworzeniu profilu logowania jednokrotnego należy przypisać go do wszystkich grup, dla których użytkownicy będą musieli uzyskać dostęp za pomocą platformy Azure. Jeśli użytkownik nie istnieje w grupie, do której zostały przypisane, Vidyard automatycznie utworzy konto użytkownika i przypisze swoją rolę w czasie rzeczywistym.

6. Wybierz grupę organizacji widoczną w **grupach dostępnych do przypisania**.

    ![Zrzut ekranu przedstawia sekcję przypisywanie konfiguracji SAML do organizacji, w której można wybrać grupę.](./media/vidyard-tutorial/configure5.png)

7. Przypisane grupy można wyświetlić w obszarze **aktualnie przypisanych grup**. Wybierz rolę dla grupy zgodnie z Twoją organizacją, a następnie kliknij przycisk **Potwierdź**.

    ![Zrzut ekranu przedstawia sekcję przypisywanie konfiguracji SAML do organizacji, w której można wybrać rolę.](./media/vidyard-tutorial/configure6.png)

    > [!NOTE]
    > Aby uzyskać więcej informacji, zapoznaj się z [tym dokumentem](https://knowledge.vidyard.com/hc/articles/360009990033-SAML-based-Single-Sign-On-SSO-in-Vidyard).

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz brittasimon@yourcompanydomain.extension . Na przykład BrittaSimon@contoso.com

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji Britta Simon do korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Vidyard.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **Vidyard**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **Vidyard**.

    ![Link Vidyard na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-vidyard-test-user"></a>Utwórz użytkownika testowego Vidyard

W tej sekcji użytkownik o nazwie Britta Simon jest tworzony w Vidyard. Vidyard obsługuje Inicjowanie obsługi użytkowników just in Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w usłudze Vidyard, zostanie utworzony nowy po uwierzytelnieniu.

>[!Note]
>Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej Vidyard](mailto:support@vidyard.com).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Vidyard w panelu dostępu należy automatycznie zalogować się do Vidyard, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)