---
title: 'Samouczek: integracja Azure Active Directory ze znakiem Adobe | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i rozwiązaniem Adobe Sign.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.openlocfilehash: a7e71f1c146b0e99ffa20cc74546e35e3ff8a5a6
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92309101"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>Samouczek: integracja Azure Active Directory ze znakiem Adobe

Z tego samouczka dowiesz się, jak zintegrować rozwiązanie Adobe Sign z usługą Azure Active Directory (Azure AD).
Zintegrowanie rozwiązania Adobe Sign z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować w usłudze Azure AD, kto ma dostęp do rozwiązania Adobe Sign.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do rozwiązania Adobe Sign (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji usługi Azure AD z rozwiązaniem Adobe Sign potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja rozwiązania Adobe Sign z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Rozwiązanie Adobe Sign obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług**

## <a name="adding-adobe-sign-from-the-gallery"></a>Dodawanie rozwiązania Adobe Sign z galerii

Aby skonfigurować integrację rozwiązania Adobe Sign z usługą Azure AD, należy z poziomu galerii dodać to rozwiązanie do listy zarządzanych aplikacji SaaS.

**Aby dodać rozwiązanie Adobe Sign z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Adobe Sign**, wybierz pozycję **Adobe Sign** z panelu wyników, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Rozwiązanie Adobe Sign na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z rozwiązaniem Adobe Sign, korzystając z danych użytkownika testowego **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem rozwiązania Adobe Sign.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z rozwiązaniem Adobe Sign, należy wykonać czynności opisane w poniższych blokach konstrukcyjnych:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w rozwiązaniu Adobe Sign](#configure-adobe-sign-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego w rozwiązaniu Adobe Sign](#create-adobe-sign-test-user)** — aby w aplikacji Adobe Sign utworzyć odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w rozwiązaniu Adobe Sign, wykonaj następujące czynności:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **Adobe Sign** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje o domenie i adresach URL logowania jednokrotnego rozwiązania Adobe Sign](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<companyname>.echosign.com/`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<companyname>.echosign.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. W celu uzyskania tych wartości skontaktuj się z [zespołem pomocy technicznej klienta rozwiązania Adobe Sign](https://helpx.adobe.com/in/contact/support.html). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie aplikacji Adobe Sign** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-adobe-sign-single-sign-on"></a>Konfigurowanie logowania jednokrotnego rozwiązania Adobe Sign

1. Przed rozpoczęciem konfiguracji skontaktuj się z [zespołem pomocy technicznej programu Adobe Sign](https://helpx.adobe.com/in/contact/support.html) , aby dodać swoją domenę na liście dozwolonych podpisów Adobe. Poniżej przedstawiono sposób dodawania domeny:

    a. [Zespół pomocy technicznej klienta rozwiązania Adobe Sign](https://helpx.adobe.com/in/contact/support.html) wysyła do Ciebie losowo wygenerowany token. W przypadku Twojej domeny token będzie wyglądał podobnie do następującego: **adobe-sign-verification= xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx**

    b. Opublikuj token weryfikacji w rekordzie tekstu DNS tekstu i powiadom [zespół pomocy technicznej klienta rozwiązania Adobe Sign](https://helpx.adobe.com/in/contact/support.html).
    
    > [!NOTE]
    > Może to potrwać co najmniej kilka dni. Pamiętaj, że opóźnienia propagacji systemu DNS oznaczają, że wartość opublikowana w systemie DNS może być niewidoczna przez godzinę lub dłużej. Administrator IT powinien mieć odpowiednią wiedzę na temat sposobu publikowania tego tokenu w rekordzie tekstu DNS.
    
    c. Gdy powiadomisz [zespół pomocy technicznej klienta rozwiązania Adobe Sign](https://helpx.adobe.com/in/contact/support.html) za pośrednictwem biletu pomocy technicznej, po opublikowaniu tokenu członkowie tego zespołu zweryfikują domenę i dodadzą ją do konta.
    
    d. Mówiąc ogólnie, poniżej przedstawiono sposób publikowania tokenu rekordu DNS:

    * Zaloguj się do konta domeny.
    * Znajdź stronę służącą do aktualizowania rekordów DNS. Ta strona może mieć nazwę Zarządzanie systemem DNS, Zarządzanie serwerem nazw lub Ustawienia zaawansowane.
    * Znajdź rekordy TXT dla domeny.
    * Dodaj rekord TXT z pełną wartością tokenu dostarczony przez firmy Adobe.
    * Zapisz zmiany.

1. W innym oknie przeglądarki internetowej zaloguj się do firmowej witryny aplikacji Adobe Sign jako administrator.

1. W menu SAML wybierz pozycję **Ustawienia konta**  >  **Ustawienia SAML**.
   
    ![Zrzut ekranu strony ustawień SAML podpisywania programu Adobe](./media/adobe-echosign-tutorial/ic789520.png "Konto")

1. W sekcji **Ustawienia języka SAML** wykonaj następujące czynności:
  
   ![Zrzut ekranu, który wyróżnia ustawienia SAML, w tym SAML obowiązkowy.](./media/adobe-echosign-tutorial/ic789521.png "Ustawienia SAML")
   
   ![Zrzut ekranu ustawień protokołu SAML](./media/adobe-echosign-tutorial/ic789522.png "Ustawienia SAML")

   a. W obszarze **Tryb SAML** wybierz pozycję **SAML — obowiązkowe**.
   
   b. Wybierz pozycję **Zezwalaj administratorom konta Echosign na logowanie się przy użyciu ich poświadczeń rozwiązania Echosign**.
   
   c. W obszarze **Tworzenie użytkownika** wybierz pozycję **Automatycznie dodawaj użytkowników uwierzytelnionych przy użyciu języka SAML**.

   d. Wklej **identyfikator usługi Azure AD** skopiowany z witryny Azure Portal w polu tekstowym **Identyfikator jednostki dostawcy tożsamości**.
    
   e. Wklej **adres URL logowania** skopiowany z witryny Azure Portal w polu tekstowym **Adres URL logowania dostawcy tożsamości**.
   
   f. Wklej **adres URL wylogowywania** skopiowany z witryny Azure Portal w polu tekstowym **Adres URL wylogowywania dostawcy tożsamości**.

   przykład Otwórz pobrany plik **Certificate(Base64)** w Notatniku. Skopiuj jego zawartość do schowka, a następnie wklej go w polu tekstowym **Certyfikat dostawcy tożsamości**.

   h. Wybierz pozycję **Zapisz zmiany**.

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

W tej sekcji włączysz dla użytkownika Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji Adobe Sign.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję **Wszystkie aplikacje**, a następnie wybierz pozycję **Adobe Sign**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wpisz i wybierz pozycję **Adobe Sign**.

    ![Link rozwiązania Adobe Sign na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-adobe-sign-test-user"></a>Tworzenie użytkownika testowego rozwiązania Adobe Sign

Aby umożliwić użytkownikom usługi Azure AD logowanie się w rozwiązaniu Adobe Sign, należy ich aprowizować w tym rozwiązaniu. Jest to zadanie wykonywane ręcznie.

>[!NOTE]
>Aby aprowizować konta użytkowników usługi Azure AD, można użyć dowolnych innych interfejsów API lub narzędzi tworzenia konta użytkownika aplikacji Adobe Sign dostępnych w tym rozwiązaniu. 

1. Zaloguj się do firmowej witryny aplikacji**Adobe Sign** jako administrator.

2. W menu w górnej części strony wybierz pozycję **Konto**. Następnie w okienku po lewej stronie wybierz pozycję **Użytkownicy & grupy**  >  **Utwórz nowego użytkownika**.
   
    ![Zrzut ekranu przedstawiający witrynę firmy Adobe Podpisz, z kontami, użytkownikami &grupami i Utwórz nowy użytkownik wyróżniony](./media/adobe-echosign-tutorial/ic789524.png "Konto")
   
3. W sekcji **Tworzenie nowego użytkownika** wykonaj następujące czynności:
   
    ![Zrzut ekranu przedstawiający sekcję Tworzenie nowego użytkownika](./media/adobe-echosign-tutorial/ic789525.png "Utwórz użytkownika")
   
    a. Wpisz wartości pól **Adres e-mail**, **Imię** i **Nazwisko** prawidłowego konta usługi Azure AD, które chcesz aprowizować, w powiązanych polach tekstowych.
   
    b. Wybierz pozycję **Utwórz użytkownika**.

>[!NOTE]
>Właściciel konta usługi Azure Active Directory otrzymuje wiadomość e-mail z linkiem umożliwiającym potwierdzenie konta, zanim stanie się ono aktywne. 

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Adobe Sign w panelu dostępu powinno nastąpić automatyczne zalogowanie do rozwiązania Adobe Sign, dla którego skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)