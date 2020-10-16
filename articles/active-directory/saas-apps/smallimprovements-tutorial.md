---
title: 'Samouczek: integracja Azure Active Directory z małymi ulepszeniami | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i małymi ulepszeniami.
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
ms.openlocfilehash: ee5927e19dfb6864d7edcde9dceadfd744d7ddbf
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92126622"
---
# <a name="tutorial-azure-active-directory-integration-with-small-improvements"></a>Samouczek: integracja Azure Active Directory z małymi ulepszeniami

W tym samouczku dowiesz się, jak zintegrować małe udoskonalenia z usługą Azure Active Directory (Azure AD).
Integracja małych ulepszeń z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do małych ulepszeń.
* Możesz umożliwić użytkownikom automatyczne logowanie do małych ulepszeń (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację z usługą Azure AD przy użyciu małych ulepszeń, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Niewielka ulepszona subskrypcja obsługująca Logowanie jednokrotne

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Małe ulepszenia obsługują zainicjowanie rejestracji jednokrotnej w programie **SP**

## <a name="adding-small-improvements-from-the-gallery"></a>Dodawanie małych ulepszeń z galerii

Aby skonfigurować integrację małych ulepszeń z usługą Azure AD, należy dodać do listy aplikacji zarządzanych SaaS małe ulepszenia z galerii.

**Aby dodać małe ulepszenia z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **małe udoskonalenia**, wybierz opcję **małe ulepszenia** w panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

     ![Małe ulepszenia na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD przy użyciu niewielkich ulepszeń w oparciu o użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w małych ulepszeniach.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu małych ulepszeń, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj małe ulepszenia rejestracji](#configure-small-improvements-single-sign-on)** jednokrotnej — aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Twórz małe udoskonalenia użytkownika testowego](#create-small-improvements-test-user)** , aby uzyskać odpowiednik Britta Simon w małych ulepszeniach, które są połączone z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD przy użyciu małych ulepszeń, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie **mała poprawa** integracji aplikacji wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Małe ulepszenia domen i adresów URL Logowanie jednokrotne — informacje](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<subdomain>.small-improvements.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<subdomain>.small-improvements.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z [małym ulepszeniem zespołu pomocy technicznej klienta](mailto:support@small-improvements.com) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie małych ulepszeń** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-small-improvements-single-sign-on"></a>Skonfiguruj małe udoskonalenia Sign-On pojedynczych

1. W innym oknie przeglądarki Zaloguj się do swojej małej ulepszanej witryny firmowej jako administrator.

1. Na głównym pulpicie nawigacyjnym kliknij przycisk **Administracja** po lewej stronie.

    ![Zrzut ekranu przedstawia wybrany przycisk Administracja.](./media/smallimprovements-tutorial/tutorial_smallimprovements_06.png) 

1. Kliknij przycisk protokołu **SAML SSO** z sekcji **Integrations (integracje** ).

    ![Zrzut ekranu przedstawia ikonę SAML S S O wybraną w obszarze integracji.](./media/smallimprovements-tutorial/tutorial_smallimprovements_07.png) 

1. Na stronie Konfiguracja logowania jednokrotnego wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia stronę instalatora S-O, na której można wprowadzić podane wartości.](./media/smallimprovements-tutorial/tutorial_smallimprovements_08.png)  

    a. W polu tekstowym **punkt końcowy HTTP** wklej wartość **adresu URL logowania**, który został skopiowany z Azure Portal.

    b. Otwórz pobrany certyfikat w Notatniku, skopiuj zawartość, a następnie wklej ją do pola tekstowego **certyfikatu x509** . 

    c. Jeśli chcesz, aby użytkownicy mieli dostęp do logowania jednokrotnego i uwierzytelniania za pośrednictwem formularza, zaznacz opcję **Włącz dostęp za pomocą nazwy logowania/hasła** .  

    d. Wprowadź odpowiednią wartość, aby nazwać przycisk logowania jednokrotnego w polu tekstowym **monitu SAML** .  

    e. Kliknij pozycję **Zapisz**.

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

W tej sekcji Britta Simon do korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do małych ulepszeń.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **małe ulepszenia**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz opcję **małe ulepszenia**.

    ![Link małych ulepszeń na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-small-improvements-test-user"></a>Utwórz niewielką poprawę użytkownika testowego

Aby umożliwić użytkownikom usługi Azure AD logowanie się do małych ulepszeń, muszą one być obsługiwane w małych ulepszeniach. W przypadku małych ulepszeń Inicjowanie obsługi jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do swojej małej poprawy witryny firmy jako administrator.

1. Na stronie głównej przejdź do menu po lewej stronie, kliknij pozycję **Administracja**.

1. Kliknij przycisk **Katalog użytkownika** w sekcji Zarządzanie użytkownikami.

    ![Zrzut ekranu przedstawia katalog użytkownika wybrany z omówienia administrowania.](./media/smallimprovements-tutorial/tutorial_smallimprovements_10.png) 

1. Kliknij pozycję **Dodaj użytkowników**.

    ![Zrzut ekranu przedstawia przycisk Dodaj użytkowników.](./media/smallimprovements-tutorial/tutorial_smallimprovements_11.png) 

1. W oknie dialogowym **Dodawanie użytkowników** wykonaj następujące czynności: 

    ![Zrzut ekranu przedstawia okno dialogowe Dodawanie użytkowników, w którym można wprowadzić podane wartości.](./media/smallimprovements-tutorial/tutorial_smallimprovements_12.png)

    a. Wprowadź **imię i nazwisko** użytkownika, np. **Britta**.

    b. Wprowadź **nazwisko** użytkownika, np. **Simon**.

    c. Wprowadź **adres e-mail** użytkownika **brittasimon@contoso.com** .

    d. Możesz również wprowadzić wiadomość osobistą w polu **Wyślij wiadomość e-mail z powiadomieniem** . Jeśli nie chcesz wysyłać powiadomienia, usuń zaznaczenie tego pola wyboru.

    e. Kliknij pozycję **Utwórz użytkowników**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka małych ulepszeń w panelu dostępu należy automatycznie zalogować się do małych ulepszeń, dla których skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)