---
title: 'Samouczek: integracja Azure Active Directory z łącznikiem meta Networks | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Meta Networks Connector.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.openlocfilehash: 941218f9a35a0a242612ae1d6230c642129c8259
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91856895"
---
# <a name="tutorial-azure-active-directory-integration-with-meta-networks-connector"></a>Samouczek: integracja Azure Active Directory z łącznikiem meta Networks

Z tego samouczka dowiesz się, jak zintegrować aplikację Meta Networks Connector z usługą Azure Active Directory (Azure AD).
Integracja aplikacji Meta Networks Connector z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD możesz kontrolować, kto ma dostęp do aplikacji Meta Networks Connector.
* Swoim użytkownikom możesz zezwolić na automatyczne logowanie do aplikacji Meta Networks Connector (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z aplikacją Meta Networks Connector, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji Meta Networks Connector z włączoną obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Meta Networks Connector obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług** oraz **dostawcę tożsamości**
 
* Aplikacja Meta Networks Connector obsługuje aprowizację użytkowników typu **just in time**

## <a name="adding-meta-networks-connector-from-the-gallery"></a>Dodawanie aplikacji Meta Networks Connector z galerii

Aby skonfigurować integrację aplikacji Meta Networks Connector z usługą Azure AD, należy dodać aplikację Meta Networks Connector z galerii do swojej listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację Meta Networks Connector z galerii, wykonaj następujące kroki:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz wartość **Meta Networks Connector**, wybierz pozycję **Meta Networks Connector** z panelu wyników, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Aplikacja Meta Networks Connector na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w aplikacji Meta Networks Connector, korzystając z danych testowego użytkownika **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji Meta Networks Connector.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD w aplikacji Meta Networks Connector, należy wykonać czynności opisane w poniższych blokach konstrukcyjnych:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w aplikacji Meta Networks Connector](#configure-meta-networks-connector-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego aplikacji Meta Networks Connector](#create-meta-networks-connector-test-user)** — aby w aplikacji Meta Networks Connector utworzyć odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w aplikacji Meta Networks Connector, wykonaj następujące czynności:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **Meta Networks Connector** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia podstawową konfigurację języka SAML, w której można wprowadzić identyfikator, odpowiedź U R L i wybrać pozycję Zapisz.](common/idp-intiated.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/saml/metadata`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/sso/saml`

5. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    ![Zrzut ekranu przedstawia ustawienie dodatkowego U R LS, gdzie można wprowadzić znak U R L.](common/both-advanced-urls.png)

    a. W polu tekstowym **adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/login`

    b. W polu tekstowym **Stan przekaźnika** wpisz adres URL, korzystając z następującego wzorca: `https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/#/`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości, używając rzeczywistego identyfikatora, adresu URL odpowiedzi i adresu URL logowania, które zostały opisane w dalszej części tego samouczka.

6. Aplikacja Meta Networks Connector oczekuje asercji SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenów języka SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Kliknij przycisk **Edytuj** ikonę, aby otworzyć okno dialogowe **atrybuty użytkownika** .

    ![Zrzut ekranu przedstawia atrybuty użytkownika z wybraną ikoną Edytuj.](common/edit-attribute.png)
    
7. Oprócz powyższych, aplikacja Meta Networks Connector oczekuje jeszcze kilku atrybutów, które powinny zostać przekazane w odpowiedzi SAML. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** wykonaj następujące czynności, aby dodać atrybut tokenu SAML, jak pokazano w poniższej tabeli:
    
    | Nazwa | Atrybut źródłowy | Przestrzeń nazw|
    | ---------------| --------------- | -------- |
    | firstname | user.givenname | |
    | lastname | user.surname | |
    | emailaddress| user.mail| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | name | user.userprincipalname| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | phone | user.telephonenumber | |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![Zrzut ekranu przedstawia oświadczenia użytkownika z opcją dodania nowego oświadczenia.](common/new-save-attribute.png)

    ![Zrzut ekranu przedstawia okno dialogowe Zarządzanie oświadczeniami użytkowników, w którym można wprowadzić podane wartości.](common/new-attribute-details.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    c. Pozostaw pole **Przestrzeń nazw** puste.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij przycisk **OK** .

    przykład Kliknij przycisk **Zapisz**.

8. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

9. W sekcji **Konfigurowanie aplikacji Meta Networks Connector** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-meta-networks-connector-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w aplikacji Meta Networks Connector

1. Otwórz nową kartę w przeglądarce i zaloguj się na swoim koncie administratora aplikacji Meta Networks Connector.
    
    > [!NOTE]
    > Aplikacja Meta Networks Connector to bezpieczny system. Przed uzyskaniem dostępu do portalu należy uzyskać publiczny adres IP, który został dodany do listy dozwolonych. Aby uzyskać swój publiczny adres IP, skorzystaj z [tego](https://whatismyipaddress.com/) linku. Wyślij adres IP do [zespołu obsługi klienta łącznika meta Networks](mailto:support@metanetworks.com) , aby uzyskać adres IP dodany do listy dozwolonych.
    
2. Przejdź do obszaru **Administrator**, a następnie wybierz pozycję **Settings (Ustawienia)**.
    
    ![Zrzut ekranu przedstawia ustawienia wybrane z menu Administracja.](./media/metanetworksconnector-tutorial/configure3.png)
    
3. Upewnij się, że ustawienia **Log Internet Traffic (Rejestruj ruch internetowy)** i **Force VPN MFA (Wymuś uwierzytelnianie wieloskładnikowe w sieci VPN)** są wyłączone.
    
    ![Zrzut ekranu przedstawia wyłączenie tych ustawień.](./media/metanetworksconnector-tutorial/configure1.png)
    
4. Przejdź do obszaru **Administrator**, a następnie wybierz pozycję **SAML**.
    
    ![Zrzut ekranu przedstawia element SAML wybrany z menu Administracja.](./media/metanetworksconnector-tutorial/configure4.png)
    
5. Wykonaj następujące kroki opisane na stronie **DETAILS (SZCZEGÓŁY)**:
    
    ![Zrzut ekranu przedstawia stronę szczegółów, na której można wprowadzić podane wartości.](./media/metanetworksconnector-tutorial/configure2.png)
    
    a. Skopiuj wartość pola **SSO URL (Adres URL logowania jednokrotnego)** i wklej ją w polu tekstowym **Sign-In URL (Adres URL logowania)** w sekcji **Meta Networks Connector Domain and URLs (Adresy URL i domena aplikacji Meta Networks Connector)**.
    
    b. Skopiuj wartość pola **Recipient URL (Adres URL adresata)** i wklej ją w polu tekstowym **Reply URL (Adres URL odpowiedzi)** w sekcji **Meta Networks Connector Domain and URLs (Adresy URL i domena aplikacji Meta Networks Connector)**.
    
    c. Skopiuj wartość pola **Audience URI (SP Entity ID) (Identyfikator URI — identyfikator jednostki dostawcy usług)** i wklej ją w polu tekstowym **Identifier (Entity ID) (Identyfikator — identyfikator jednostki)** w sekcji **Meta Networks Connector Domain and URLs (Adresy URL i domena aplikacji Meta Networks Connector)**.
    
    d. Włączanie protokołu SAML
    
6. Na karcie **GENERAL (OGÓLNE)** wykonaj następujące kroki:

    ![Zrzut ekranu przedstawia stronę ogólne, na której można wprowadzić podane wartości.](./media/metanetworksconnector-tutorial/configure5.png)

    a. W polu **Identity Provider Single Sign-On URL (Adres URL logowania jednokrotnego dostawcy tożsamości)** wklej wartość pola **Adres URL logowania** skopiowaną z witryny Azure Portal.

    b. W polu **Identity Provider Issuer (Wystawca dostawcy tożsamości)** wklej wartość pola **Identyfikator usługi Azure AD** skopiowaną z witryny Azure Portal.

    c. Otwórz w Notatniku certyfikat pobrany z witryny Azure Portal, a następnie wklej go w polu tekstowym **X.509 Certificate (Certyfikat X.509)**.

    d. Włącz **aprowizację typu just in time**.

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

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz dla użytkownika Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji Meta Networks Connector.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję **Wszystkie aplikacje**, a następnie wybierz pozycję **Meta Networks Connector**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Meta Networks Connector**.

    ![Link do aplikacji Meta Networks Connector na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

### <a name="create-meta-networks-connector-test-user"></a>Tworzenie użytkownika testowego aplikacji Meta Networks Connector

W tej sekcji w aplikacji Meta Networks Connector jest tworzony użytkownik o nazwie Britta Simon. Aplikacja Meta Networks Connector obsługuje aprowizację typu just in time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w aplikacji Meta Networks Connector, zostanie utworzony podczas próby uzyskania dostępu do tej aplikacji.

>[!Note]
>Jeśli konieczne jest ręczne utworzenie użytkownika, skontaktuj się z [zespołem pomocy technicznej klienta aplikacji Meta Networks Connector](mailto:support@metanetworks.com).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Meta Networks Connector na panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Meta Networks Connector, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

