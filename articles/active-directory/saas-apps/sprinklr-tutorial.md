---
title: 'Samouczek: integracja Azure Active Directory z zraszaczem | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i zraszaczem.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: c6a7784d052a03971fad84c90cdd70cd16b4a872
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92122572"
---
# <a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>Samouczek: integracja Azure Active Directory z zraszaczem

W tym samouczku dowiesz się, jak zintegrować program zraszacz z usługą Azure Active Directory (Azure AD).
Integracja programu zraszacz z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do programu zraszacz.
* Możesz umożliwić użytkownikom automatyczne logowanie do programu zraszaczy (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację z usługą Azure AD za pomocą programu zraszacz, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja z włączonym logowaniem jednokrotnym

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Program zraszacz obsługuje logowanie jednokrotne w usłudze **SP**

## <a name="adding-sprinklr-from-the-gallery"></a>Dodawanie programu zraszacz z galerii

Aby skonfigurować integrację programu zraszacz z usługą Azure AD, musisz dodać program zraszacz z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać program zraszacz z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz tekst **zraszaczy**, wybierz pozycję **zraszacze** w panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

     ![Zraszacz na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą programu zraszacz na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w programie Zraszaczer.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą programu zraszacz, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania](#configure-sprinklr-single-sign-on)** jednokrotnego dla programu zraszacze — w celu skonfigurowania pojedynczych ustawień Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego programu zraszaczego](#create-sprinklr-test-user)** , aby uzyskać odpowiednik Britta Simon w programie zraszacz, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą programu zraszacz, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji programu **zraszacz** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje o rejestracji jednokrotnej w domenie i adresach URL](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<subdomain>.sprinklr.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<subdomain>.sprinklr.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z [zespołem obsługi klienta programu zraszacz](https://www.sprinklr.com/contact-us/) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie instalacji zraszacz** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-sprinklr-single-sign-on"></a>Konfigurowanie jednego Sign-On zraszacza

1. W innym oknie przeglądarki sieci Web Zaloguj się do firmowej lokacji programu zraszacz jako administrator.

1. Przejdź do **pozycji \> Ustawienia administracyjne**.

    ![Administracja](./media/sprinklr-tutorial/ic782907.png "Administracja")

1. Przejdź do pozycji **Zarządzaj \> logowaniem jednokrotnym** w lewym okienku.

    ![Zarządzanie partnerem](./media/sprinklr-tutorial/ic782908.png "Zarządzanie partnerem")

1. Kliknij pozycję **+ Dodaj Logowanie jednokrotne**.

    ![Zrzut ekranu przedstawia przycisk Dodaj Logowanie jednokrotne.](./media/sprinklr-tutorial/ic782909.png "Pojedyncze Sign-Ons")

1. Na stronie **Logowanie** jednokrotne wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia stronę logowania jednokrotnego, na której można wprowadzić podane wartości.](./media/sprinklr-tutorial/ic782910.png "Pojedyncze Sign-Ons")

    a. W polu tekstowym **Nazwa** wpisz nazwę konfiguracji (na przykład: *WAADSSOTest*).

    b. Wybierz pozycję **Włączone**.

    c. Wybierz pozycję **Użyj nowego certyfikatu logowania jednokrotnego**.

    d. Otwórz certyfikat zakodowany w formacie Base-64 w Notatniku, skopiuj zawartość tego pliku do schowka, a następnie wklej go do pola tekstowego **certyfikat dostawcy tożsamości** .

    e. Wklej wartość **identyfikatora usługi Azure AD** skopiowaną z witryny Azure Portal do pola tekstowego **Identyfikator jednostki** .

    f. Wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal do pola tekstowego **adres URL logowania dostawcy tożsamości** .

    przykład Wklej wartość **adresu URL wylogowywania** skopiowaną z witryny Azure Portal do pola tekstowego **adres URL wylogowania dostawcy tożsamości** .

    h. Jako **Typ identyfikatora użytkownika SAML**, wybierz pozycję **potwierdzenie zawiera nazwę użytkownika Sprinklr.com**.

    i. Jako **lokalizację identyfikatora użytkownika SAML**wybierz pozycję **Identyfikator użytkownika w elemencie identyfikator nazwy w instrukcji subject**.

    j. Kliknij pozycję **Zapisz**.

    ![SAML](./media/sprinklr-tutorial/ic782911.png "SAML")

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

W tej sekcji Britta Simon do korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do programu zraszacz.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **zraszacz**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **zraszacz**.

    ![Link do programu zraszacz na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-sprinklr-test-user"></a>Utwórz użytkownika testowego instalacji

1. Zaloguj się do firmowej lokacji programu zraszacz jako administrator.

1. Przejdź do **pozycji \> Ustawienia administracyjne**.

    ![Administracja](./media/sprinklr-tutorial/ic782907.png "Administracja")

1. Przejdź do pozycji **Zarządzaj \> użytkownikami klienta** w okienku po lewej stronie.

    ![Zrzut ekranu przedstawia przycisk Dodaj użytkownika w obszarze Ustawienia/Użytkownicy.](./media/sprinklr-tutorial/ic782914.png "Ustawienia")

1. Kliknij pozycję **Dodaj użytkownika**.

    ![Zrzut ekranu przedstawia okno dialogowe Edytowanie użytkownika, w którym można wprowadzić podane wartości.](./media/sprinklr-tutorial/ic782915.png "Ustawienia")

1. W oknie dialogowym **Edytowanie użytkownika** wykonaj następujące czynności:

    ![Edytuj użytkownika](./media/sprinklr-tutorial/ic782916.png "Edytuj użytkownika")

    a. W **wiadomości e-mail**, **imię** i nazwisko pola **textname** wpisz informacje o koncie użytkownika usługi Azure AD, które chcesz udostępnić.

    b. Wybierz pozycję **hasło wyłączone**.

    c. Wybierz **Język**.

    d. Wybierz **Typ użytkownika**.

    e. Kliknij przycisk **Update** (Aktualizuj).

    > [!IMPORTANT]
    > Aby umożliwić użytkownikowi zalogowanie się za pośrednictwem dostawcy tożsamości, należy wybrać opcję **wyłączone hasło** . 

1. Przejdź do **roli**, a następnie wykonaj następujące czynności:

    ![Role partnerów](./media/sprinklr-tutorial/ic782917.png "Role partnerów")

    a. Z listy **globalnej** wybierz pozycję **ALL_Permissions**.  

    b. Kliknij przycisk **Update** (Aktualizuj).

> [!NOTE]
> Do udostępniania kont użytkowników usługi Azure AD można używać innych narzędzi do tworzenia kont użytkowników programu zraszacz lub interfejsów API udostępnianych przez program zraszacz.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka zraszacza w panelu dostępu należy automatycznie zalogować się do programu zraszacz, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
