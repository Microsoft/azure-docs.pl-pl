---
title: 'Samouczek: integracja Azure Active Directory z pakietem Questetra BPM Suite | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między usługami Azure Active Directory i Questetra BPM Suite.
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
ms.openlocfilehash: 13f05005529d3983e042398f5274fd7f981b8c8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92516600"
---
# <a name="tutorial-azure-active-directory-integration-with-questetra-bpm-suite"></a>Samouczek: integracja Azure Active Directory z pakietem Questetra BPM Suite

W tym samouczku dowiesz się, jak zintegrować pakiet Questetra Suite BPM z usługą Azure Active Directory (Azure AD).
Integracja pakietu Questetra BPM z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do pakietu Questetra BPM Suite.
* Możesz umożliwić użytkownikom automatyczne logowanie do Questetra BPM Suite (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z pakietem Questetra BPM, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja z włączonym logowaniem jednokrotnym w ramach usługi Questetra BPM

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Pakiet Questetra BPM obsługuje logowanie jednokrotne w usłudze **SP**

## <a name="adding-questetra-bpm-suite-from-the-gallery"></a>Dodawanie pakietu Questetra BPM z galerii

Aby skonfigurować integrację pakietu Questetra BPM z usługą Azure AD, musisz dodać pakiet Questetra BPM z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać pakiet Questetra BPM z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **QUESTETRA BPM Suite**, wybierz pozycję **Questetra BPM Suite** z panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

     ![Questetra BPM Suite na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą Questetra BPM Suite na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w pakiecie Questetra BPM.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą zestawu Questetra BPM, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie](#configure-questetra-bpm-suite-single-sign-on)** jednokrotne w usłudze Questetra BPM, aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego pakietu QUESTETRA BPM Suite](#create-questetra-bpm-suite-test-user)** , aby uzyskać odpowiednik Britta Simon w Questetra BPM Suite, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą pakietu Questetra BPM Suite, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Questetra BPM Suite** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Questetra BPM Suite — informacje o rejestracji jednokrotnej i adresach URL](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<subdomain>.questetra.net/saml/SSO/alias/bpm`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<subdomain>.questetra.net/`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj je, używając faktycznego adresu URL i identyfikatora logowania. Te wartości można uzyskać z sekcji **informacje dotyczące programu Sp** w witrynie firmy **Questetra Suite** w wersji BPM, która została omówiona w dalszej części samouczka lub skontaktuj się z działem [pomocy technicznej klienta Questetra BPM Suite](https://www.questetra.com/contact/). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie QUESTETRA BPM Suite** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-questetra-bpm-suite-single-sign-on"></a>Konfigurowanie jednego Sign-On zestawu Questetra BPM

1. W innym oknie przeglądarki sieci Web Zaloguj się do swojej witryny firmowej **QUESTETRA BPM Suite** jako administrator.

2. W menu u góry kliknij pozycję **Ustawienia systemowe**. 
   
    ![Zrzut ekranu przedstawia ustawienia systemu wybrane z firmowej witryny Questetra BPM Suite.][10]

3. Aby otworzyć stronę **SingleSignOnSAML** , kliknij pozycję **Logowanie jednokrotne (SAML)**. 
   
    ![Zrzut ekranu przedstawia wybrany element S (SAML).][11]

4. W witrynie firmy z **pakietem QUESTETRA BPM Suite** w sekcji **Informacje o programie Sp** wykonaj następujące czynności:

    a. Skopiuj **adres URL usługi ACS**, a następnie wklej go do pola tekstowego **Zaloguj się w adresie URL** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.
    
    b. Skopiuj **Identyfikator jednostki**, a następnie wklej go do pola tekstowego **identyfikatora** w sekcji **podstawowe konfiguracje języka SAML** z Azure Portal.

5. W witrynie firmowej **pakietu QUESTETRA BPM Suite** wykonaj następujące czynności: 
   
    ![Konfigurowanie logowania jednokrotnego][15]
   
    a. Wybierz pozycję **Włącz logowanie jednokrotne**.
   
    b. W polu tekstowym **Identyfikator jednostki** wklej wartość identyfikatora usługi **Azure AD** , który został skopiowany z Azure Portal.
    
    c. W polu tekstowym **adres URL strony logowania** wklej wartość **adresu URL logowania** skopiowanego z Azure Portal.
    
    d. W polu tekstowym **adres URL strony wylogowywania** wklej wartość **adresu URL wylogowania** skopiowanego z Azure Portal.
    
    e. W polu tekstowym **Format NameID** wpisz `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress` .

    f. Otwórz certyfikat zakodowany w formacie **Base-64** w Notatniku pobranym z Azure Portal Skopiuj zawartość tego pliku do schowka, a następnie wklej go do pola tekstowego **certyfikat walidacji** . 

    przykład Kliknij pozycję **Zapisz**.

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

Ta sekcja umożliwia włączenie usługi Britta Simon w celu korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do pakietu Questetra BPM.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **Questetra BPM Suite**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **QUESTETRA BPM Suite**.

    ![Link Suite Questetra BPM na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-questetra-bpm-suite-test-user"></a>Tworzenie użytkownika testowego pakietu Questetra BPM Suite

Celem tej sekcji jest utworzenie użytkownika o nazwie Britta Simon w pakiecie Questetra BPM.

**Aby utworzyć użytkownika o nazwie Britta Simon w pakiecie Questetra BPM, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmowej Questetra BPM Suite jako administrator.

2. Przejdź do pozycji **Ustawienia systemu > lista użytkowników > nowy użytkownik**.
 
3. W oknie dialogowym Nowy użytkownik wykonaj następujące czynności: 
   
    ![Utwórz użytkownika testowego][300] 
   
    a. W polu tekstowym **Nazwa** wpisz **nazwę** użytkownika britta.simon@contoso.com .
   
    b. W polu tekstowym **adres e-mail** wpisz **adres e-mail** użytkownika britta.simon@contoso.com .
   
    c. W polu tekstowym **hasło** wpisz **hasło** użytkownika.
    
    d. Kliknij pozycję **Dodaj nowego użytkownika**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Questetra Suite w panelu dostępu należy automatycznie zalogować się do pakietu Questetra BPM, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)

<!--Image references-->

[10]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_03.png
[11]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_04.png
[15]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_08.png
[300]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_11.png