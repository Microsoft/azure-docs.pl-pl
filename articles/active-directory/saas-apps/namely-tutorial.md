---
title: 'Samouczek: integracja Azure Active Directory z mianowicie | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory a mianowicie.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: jeedes
ms.openlocfilehash: bafec2c1b857d3220089e2ca41190987de33edb6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92519519"
---
# <a name="tutorial-azure-active-directory-integration-with-namely"></a>Samouczek: Azure Active Directory integrację z mianowicie

W ramach tego samouczka dowiesz się, jak zintegrować program z usługą Azure Active Directory (Azure AD).
Integracja z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD można kontrolować, kto ma dostęp do tego programu.
* Możesz umożliwić użytkownikom automatyczne logowanie się do usługi (Logowanie jednokrotne) przy użyciu kont w usłudze Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację z usługą Azure AD za pomocą programu, należy wykonać następujące czynności:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* A mianowicie subskrypcja z włączonym logowaniem jednokrotnym

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Mianowicie obsługuje logowanie jednokrotne zainicjowane przez usługę **SP**

## <a name="adding-namely-from-the-gallery"></a>Dodawanie a mianowicie z galerii

Aby skonfigurować integrację programu z usługą Azure AD, należy dodać go z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać a mianowicie z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **mianowicie** **, wybierz pozycję** w panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

     ![Mianowicie na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą programu w oparciu o użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w elemencie.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą programu mianowicie, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj mianowicie logowanie](#configure-namely-single-sign-on)** jednokrotne, aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz mianowicie użytkownika testowego](#create-namely-test-user)** , aby uzyskać odpowiednik Britta Simon w programie, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą programu, należy wykonać następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja z **aplikacją wybierz** opcję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![A mianowicie informacje o rejestracji jednokrotnej w domenach i adresach URL](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<subdomain>.namely.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<subdomain>.namely.com/saml/metadata`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. W tym celu skontaktuj się z [zespołem pomocy technicznej klienta](https://www.namely.com/contact/) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfiguracja a** następnie skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-namely-single-sign-on"></a>Skonfiguruj to samo Sign-On

1. W innym oknie przeglądarki Zaloguj się do witryny sieci firmowej jako administrator.

2. Na pasku narzędzi u góry kliknij pozycję **firma**.
   
    ![Zrzut ekranu przedstawia wybraną wartość firmy.](./media/namely-tutorial/tutorial_namely_06.png) 

3. Kliknij kartę **Ustawienia**.
   
    ![Zrzut ekranu przedstawia wybraną kartę Ustawienia firmy.](./media/namely-tutorial/tutorial_namely_07.png) 

4. Kliknij pozycję **SAML**.
   
    ![Zrzut ekranu przedstawia wybrany element SAML.](./media/namely-tutorial/tutorial_namely_08.png) 

5. Na stronie **Ustawienia SAML** wykonaj następujące czynności:
   
    ![Zrzut ekranu przedstawia ustawienia protokołu SAML, w których można wprowadzić podane wartości.](./media/namely-tutorial/tutorial_namely_09.png)
 
    a. Kliknij pozycję **Enable SAML** (Włącz protokół SAML). 

    b. W polu tekstowym **adres URL logowania JEDNOkrotnego dostawcy tożsamości** wklej wartość **adresu URL logowania**, która została skopiowana z Azure Portal.
    
    c. Otwórz pobrany certyfikat w Notatniku, skopiuj zawartość, a następnie wklej ją do pola tekstowego **certyfikat dostawcy tożsamości** .
     
    d. Kliknij pozycję **Zapisz**.

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

W tej sekcji Britta Simon do korzystania z logowania jednokrotnego na platformie Azure, przyznając dostęp do niego.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **co.**

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **mianowicie**.

    ![Łącze to znajduje się na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-namely-test-user"></a>Utwórz mianowicie użytkownika testowego

Celem tej sekcji jest utworzenie użytkownika o nazwie Britta Simon w temacie.

**Aby utworzyć użytkownika o nazwie Britta Simon w programie, należy wykonać następujące czynności:**

1. Zaloguj się do witryny firmy jako administrator.

2. Na pasku narzędzi u góry kliknij pozycję **osoby**.
   
    ![Zrzut ekranu przedstawia wybraną wartość osoby.](./media/namely-tutorial/tutorial_namely_10.png) 

3. Kliknij kartę **katalog** .
   
    ![Zrzut ekranu przedstawia wybraną kartę Katalog osoby.](./media/namely-tutorial/tutorial_namely_11.png) 

4. Kliknij pozycję **Dodaj nową osobę**.

    ![Zrzut ekranu przedstawia opcję Dodaj nową osobę.](./media/namely-tutorial/tutorial_namely_12.png)

5. W oknie dialogowym **Dodaj nową osobę** wykonaj następujące czynności:

    a. W polu tekstowym **imię i nazwisko** wpisz **Britta**.

    b. W polu **tekstowym nazwisko** wpisz **Simon**.

    c. W polu tekstowym adres **e-mail** wpisz **adres e-mail** BrittaSimon.

    d. Kliknij pozycję **Zapisz**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu tego kafelka w panelu dostępu należy automatycznie zalogować się do tego elementu, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)