---
title: 'Samouczek: integracja Azure Active Directory z usługą Marketo | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i usługą Marketo.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/19/2019
ms.author: jeedes
ms.openlocfilehash: 493e34ff60383ce31d185bddd684e72ff6aee3af
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92458289"
---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>Samouczek: integracja Azure Active Directory z usługą Marketo

W tym samouczku dowiesz się, jak zintegrować program Marketo z Azure Active Directory (Azure AD).
Integracja programu Marketo z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do programu Marketo.
* Możesz pozwolić użytkownikom na automatyczne logowanie do usługi Marketo (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację z usługą Azure AD za pomocą programu Marketo, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja z włączonym logowaniem jednokrotnym dla programu Marketo

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Program Market obsługuje zainicjowane przez **dostawcy tożsamości** Logowanie jednokrotne

## <a name="adding-marketo-from-the-gallery"></a>Dodawanie programu Marketo z galerii

Aby skonfigurować integrację programu Marketo z usługą Azure AD, musisz dodać program Marketo z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać program Marketo z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Marketo**, wybierz pozycję **Marketo** w panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

     ![Marketo na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą programu Marketo w oparciu o użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w programie Marketo.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą programu Marketo, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie](#configure-marketo-single-sign-on)** jednokrotne w ramach programu Marketo, aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego programu Marketo](#create-marketo-test-user)** , aby uzyskać odpowiednik usługi Britta Simon w usłudze Marketo, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą programu Marketo, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji programu **Marketo** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** wykonaj następujące kroki:

    ![Informacje o rejestracji jednokrotnej w domenach i adresach URL programu Marketo](common/idp-intiated.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://saml.marketo.com/sp`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://login.marketo.com/saml/assertion/\<munchkinid\>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Skontaktuj się z [zespołem obsługi klienta programu Marketo](https://investors.marketo.com/contactus.cfm) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie programu Marketo** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-marketo-single-sign-on"></a>Konfigurowanie pojedynczego Sign-On programu Marketo

1. Aby uzyskać Munchkin identyfikator aplikacji, zaloguj się do usługi Markete przy użyciu poświadczeń administratora i wykonaj następujące czynności:
   
    a. Zaloguj się do aplikacji Marketo przy użyciu poświadczeń administratora.
   
    b. Kliknij przycisk **administratora** w górnym okienku nawigacji.
   
    ![Zrzut ekranu przedstawia administratora wybranego w okienku nawigacji.](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Przejdź do menu integracja i kliknij **link Munchkin**.
   
    ![Zrzut ekranu przedstawia Munchkin wybrane z integracji.](./media/marketo-tutorial/tutorial_marketo_11.png)
   
    d. Skopiuj identyfikator Munchkin wyświetlany na ekranie i Uzupełnij adres URL odpowiedzi w Kreatorze konfiguracji usługi Azure AD.
   
    ![Zrzut ekranu przedstawia stronę Munchkin, na której można skopiować konto I D.](./media/marketo-tutorial/tutorial_marketo_12.png) 

2. Aby skonfigurować Logowanie jednokrotne w aplikacji, wykonaj następujące czynności:
   
    a. Zaloguj się do aplikacji Marketo przy użyciu poświadczeń administratora.
   
    b. Kliknij przycisk **administratora** w górnym okienku nawigacji.
   
    ![Zrzut ekranu przedstawia administratora wybranego w okienku nawigacji.](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Przejdź do menu integracja i kliknij pozycję **Logowanie jednokrotne**.
   
    ![Zrzut ekranu przedstawia pojedyncze Sign-On wybrane z integracji.](./media/marketo-tutorial/tutorial_marketo_07.png) 
   
    d. Aby włączyć ustawienia SAML, kliknij przycisk **Edytuj** .
   
    ![Zrzut ekranu przedstawia ustawienia S O, w których można wybrać opcję Edytuj.](./media/marketo-tutorial/tutorial_marketo_08.png) 
   
    e. **Włączone** Ustawienia pojedynczej Sign-On.
   
    f. Wklej **Identyfikator usługi Azure AD**, w polu tekstowym **Identyfikator wystawcy** .
   
    przykład W polu tekstowym **Identyfikator jednostki** wprowadź adres URL jako `http://saml.marketo.com/sp` .
   
    h. Wybierz lokalizację identyfikatora użytkownika jako **element identyfikatora nazwy**.
   
    ![Zrzut ekranu przedstawia Edytowanie ustawień SAML, w których można wprowadzić podane wartości.](./media/marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > Jeśli identyfikator użytkownika nie jest wartością UPN, Zmień wartość na karcie atrybut.
   
    i. Przekaż certyfikat pobrany z Kreatora konfiguracji usługi Azure AD. **Zapisz** ustawienia.
   
    j. Edytuj ustawienia stron przekierowania.
   
    k. Wklej **adres URL logowania** w polu tekstowym **adres URL logowania** .
   
    l. Wklej **adres URL wylogowania** w polu tekstowym **adres URL wylogowywania** .
   
    m. W **adresie URL błędu**skopiuj swój **adres URL wystąpienia programu Marketo** , a następnie kliknij przycisk **Zapisz** , aby zapisać ustawienia.
   
    ![Zrzut ekranu przedstawia okno dialogowe Edytowanie stron przekierowania, w którym można wprowadzić podane wartości.](./media/marketo-tutorial/tutorial_marketo_10.png)

3. Aby włączyć logowanie jednokrotne dla użytkowników, wykonaj następujące czynności:
   
    a. Zaloguj się do aplikacji Marketo przy użyciu poświadczeń administratora.
   
    b. Kliknij przycisk **administratora** w górnym okienku nawigacji.
   
    ![Zrzut ekranu przedstawia administratora wybranego w okienku nawigacji.](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Przejdź do menu **zabezpieczenia** , a następnie kliknij pozycję **Ustawienia logowania**.
   
    ![Zrzut ekranu przedstawia ustawienia logowania wybrane z zabezpieczenia.](./media/marketo-tutorial/tutorial_marketo_13.png)
   
    d. Zaznacz opcję **Wymagaj logowania jednokrotnego** i **Zapisz** ustawienia.
   
    ![Zrzut ekranu przedstawia ustawienia siły hasła, w których można wybrać opcję Wymagaj S S O.](./media/marketo-tutorial/tutorial_marketo_14.png)

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

W tej sekcji Britta Simon do korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do programu Marketo.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **Marketo**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **Marketo**.

    ![Link do programu Marketo na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-marketo-test-user"></a>Utwórz użytkownika testowego programu Marketo

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w programie Marketo. wykonaj następujące kroki, aby utworzyć użytkownika na platformie programu Marketo.

1. Zaloguj się do aplikacji Marketo przy użyciu poświadczeń administratora.

2. Kliknij przycisk **administratora** w górnym okienku nawigacji.
   
    ![Zrzut ekranu przedstawia administratora wybranego w okienku nawigacji.](./media/marketo-tutorial/tutorial_marketo_06.png) 

3. Przejdź do menu **zabezpieczenia** i kliknij pozycję **Użytkownicy & role**
   
    ![Zrzut ekranu przedstawia użytkowników, którzy & role wybrane z zabezpieczeń.](./media/marketo-tutorial/tutorial_marketo_19.png)  

4. Kliknij link **Zaproś nowego użytkownika** na karcie Użytkownicy
   
    ![Zrzut ekranu przedstawia pozycję Zaproś nowego użytkownika na karcie Użytkownicy.](./media/marketo-tutorial/tutorial_marketo_15.png) 

5. W Kreatorze Zaproś nowego użytkownika wypełnij następujące informacje:
   
    a. Wprowadź adres **e-mail** użytkownika w polu tekstowym
   
    ![Zrzut ekranu przedstawia pierwszy krok Zaproś Kreatora nowego użytkownika, w którym wprowadzasz informacje o użytkowniku.](./media/marketo-tutorial/tutorial_marketo_16.png)
   
    b. Wprowadź **imię** w polu tekstowym
   
    c. Wprowadź **nazwisko**  w polu tekstowym
   
    d. Kliknij przycisk **dalej** .

6. Na karcie **uprawnienia** wybierz pozycję **roli użytkownika** , a następnie kliknij przycisk **dalej** .
   
    ![Zrzut ekranu przedstawia pierwszy krok Zaproś Kreatora nowego użytkownika, w którym wprowadzasz uprawnienia.](./media/marketo-tutorial/tutorial_marketo_17.png)
7. Kliknij przycisk **Wyślij** , aby wysłać zaproszenie użytkownika
   
    ![Zrzut ekranu przedstawia pierwszy krok Zaproś Kreatora nowego użytkownika, w którym wprowadzasz wiadomość.](./media/marketo-tutorial/tutorial_marketo_18.png)

8. Użytkownik otrzymuje powiadomienie e-mail i kliknie łącze i zmieni hasło w celu aktywowania konta. 

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka usługi Marketo w panelu dostępu należy automatycznie zalogować się do programu Marketo, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)