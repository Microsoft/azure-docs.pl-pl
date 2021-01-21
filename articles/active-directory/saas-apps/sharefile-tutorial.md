---
title: 'Samouczek: integracja Azure Active Directory z usługą Citrix ShareFile | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Citrix ShareFile.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/18/2021
ms.author: jeedes
ms.openlocfilehash: ebd07990767d91c88f245b4ed7f8a66f93de0b85
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98631418"
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>Samouczek: integracja Azure Active Directory z usługą Citrix ShareFile

Z tego samouczka dowiesz się, jak zintegrować aplikację Citrix ShareFile z usługą Azure Active Directory (Azure AD).
Integracja aplikacji Citrix ShareFile z usługą Azure AD oferuje następujące korzyści:

* W usłudze Azure AD możesz kontrolować, kto ma dostęp do aplikacji Citrix ShareFile.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do aplikacji Citrix ShareFile (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji usługi Azure AD z aplikacją Citrix ShareFile potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz w [tym miejscu](https://azure.microsoft.com/pricing/free-trial/)uzyskać miesięczną wersję próbną.
* Subskrypcja obsługująca Logowanie jednokrotne w programie Citrix ShareFile.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Citrix ShareFile obsługuje logowanie jednokrotne inicjowane przez **SP**

## <a name="adding-citrix-sharefile-from-the-gallery"></a>Dodawanie aplikacji Citrix ShareFile z galerii

Aby skonfigurować integrację aplikacji Citrix ShareFile z usługą Azure AD, musisz dodać aplikację Citrix ShareFile z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Citrix ShareFile** w polu wyszukiwania.
1. Wybierz pozycję **Citrix ShareFile** w panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-citrix-sharefile"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla programu Citrix ShareFile

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z aplikacją Citrix ShareFile, korzystając z danych testowego użytkownika **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji Citrix ShareFile.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą programu Citrix ShareFile, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    
    1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
    1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
2. **[Skonfiguruj Logowanie jednokrotne w usłudze Citrix ShareFile](#configure-citrix-sharefile-sso)** , aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
    1. **[Tworzenie użytkownika testowego aplikacji Citrix ShareFile](#create-citrix-sharefile-test-user)** — aby mieć w aplikacji Citrix ShareFile odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
3. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **Citrix ShareFile** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<tenant-name>.sharefile.com/saml/login`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, korzystając z następującego wzorca:

    - `https://<tenant-name>.sharefile.com`
    - `https://<tenant-name>.sharefile.com/saml/info`
    - `https://<tenant-name>.sharefile1.com/saml/info`
    - `https://<tenant-name>.sharefile1.eu/saml/info`
    - `https://<tenant-name>.sharefile.eu/saml/info`

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: 
    
    - `https://<tenant-name>.sharefile.com/saml/acs`
    - `https://<tenant-name>.sharefile.eu/saml/<URL path>`
    - `https://<tenant-name>.sharefile.com/saml/<URL path>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami adresu URL logowania, identyfikatora i adresu URL odpowiedzi. Skontaktuj się z [zespołem pomocy technicznej klienta Citrix ShareFile](https://www.citrix.co.in/products/citrix-content-collaboration/support.html), aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Skonfiguruj aplikację Citrix ShareFile** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Citrix ShareFile.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Citrix ShareFile**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-citrix-sharefile-sso"></a>Konfigurowanie logowania jednokrotnego dla serwera Citrix ShareFile

1. Aby zautomatyzować konfigurację w programie **Citrix ShareFile**, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki kliknij pozycję **Skonfiguruj program Citrix ShareFile** , aby skierować do aplikacji Citrix ShareFile. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do programu Citrix ShareFile. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i zautomatyzuje kroki 3–7.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować program Citrix ShareFile, w innym oknie przeglądarki sieci Web Zaloguj się do witryny firmy Citrix ShareFile jako administrator.

1. Na **pulpicie nawigacyjnym** kliknij pozycję **Ustawienia** , a następnie wybierz pozycję **Ustawienia administratora**.

    ![Administracja](./media/sharefile-tutorial/settings.png)

1. W ustawieniach administratora przejdź do   ->  **zasad zabezpieczeń & logowania** zabezpieczeń.
   
    ![Administrowanie kontem](./media/sharefile-tutorial/settings-security.png "Administrowanie kontem")

1. Na stronie dialogowej **Single Sign-On / SAML 2.0 Configuration** (Logowanie jednokrotne / Konfiguracja SAML 2.0) w obszarze **Basic Settings** (Ustawienia podstawowe) wykonaj następujące kroki:
   
    ![Rejestracja jednokrotna](./media/sharefile-tutorial/saml-configuration.png "Logowanie jednokrotne")
   
    a. Wybierz pozycję **tak** w polu **Włącz SAML**.

    b. Skopiuj wartość **Identyfikator wystawcy ShareFile/jednostki** i wklej ją do pola **Identyfikator URL** w oknie dialogowym **podstawowe konfiguracje SAML** w Azure Portal.
    
    c. W polu tekstowym **Your IDP Issuer/ Entity ID** (Wystawca IDP / Identyfikator jednostki) wklej wartość **identyfikatora usługi Azure AD** skopiowaną z witryny Azure Portal.

    d. Kliknij pozycję **Change** (Zmień) obok pola **X.509 Certificate** (Certyfikat X.509), a następnie przekaż certyfikat pobrany z witryny Azure Portal.
    
    e. W polu tekstowym **Login URL** (Adres URL logowania) wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.
    
    f. W polu tekstowym **adres URL wylogowywania** wklej wartość **adresu URL wylogowywania** skopiowanego z Azure Portal.

    przykład W **ustawieniach opcjonalnych** wybierz **kontekst uwierzytelniania zainicjowanego przez program SP** jako **nazwę użytkownika i hasło** oraz **dokładne**.

5. Kliknij pozycję **Zapisz**.

## <a name="create-citrix-sharefile-test-user"></a>Tworzenie użytkownika testowego aplikacji Citrix ShareFile

1. Zaloguj się do dzierżawy usługi **Citrix ShareFile**.

2. Kliknij pozycję **osoby**  ->  **Zarządzaj użytkownikami Strona główna**  ->  **Utwórz nowych użytkowników**  ->  **Utwórz pracownika**.
   
    ![Utwórz pracownika](./media/sharefile-tutorial/create-user.png "Utwórz pracownika")

3. W sekcji **Basic Information** (Informacje podstawowe) wykonaj następujące kroki:
   
    ![Informacje podstawowe](./media/sharefile-tutorial/user-form.png "Informacje podstawowe")
   
    a. W polu tekstowym **First Name** (Imię) wpisz **imię** użytkownika jako **Britta**.
   
    b.  W polu tekstowym **Last Name** (Nazwisko) wpisz **nazwisko** użytkownika jako **Simon**.
   
    c. W polu tekstowym **adres e-mail** wpisz adres E-mail Britta Simon jako **brittasimon \@ contoso.com**.

4. Kliknij pozycję **Dodaj użytkownika**.
  
    >[!NOTE]
    >Właściciel konta usługi Azure AD otrzyma wiadomość e-mail i użyje linku, aby potwierdzić swoje konto, zanim stanie się ono aktywne. Aby aprowizować konta użytkowników usługi Azure AD, możesz użyć dowolnych innych interfejsów API lub narzędzi do tworzenia kont użytkowników udostępnianych przez usługę Citrix ShareFile.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji.

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania Citrix ShareFile, w którym można zainicjować przepływ logowania.

* Przejdź bezpośrednio do adresu URL logowania Citrix ShareFile i zainicjuj w nim przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka Citrix ShareFile w obszarze Moje aplikacje zostanie on przekierowany do adresu URL logowania Citrix ShareFile. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu programu Citrix ShareFile można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).