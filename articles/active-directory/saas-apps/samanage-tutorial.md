---
title: 'Samouczek: integracja Azure Active Directory z obsługą techniczną SolarWinds (wcześniej Samanage) | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i SolarWinds Service Desk (wcześniej Samanage).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.openlocfilehash: 8d4c19e1ce10ed618cda167cd6fa7efedf4111d0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90707581"
---
# <a name="tutorial-azure-active-directory-integration-with-solarwinds-service-desk-previously-samanage"></a>Samouczek: integracja Azure Active Directory z obsługą techniczną SolarWinds (wcześniej Samanage)

W tym samouczku dowiesz się, jak zintegrować usługę SolarWinds z usługą Azure Active Directory (Azure AD).
Integracja SolarWinds z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do usługi SolarWinds.
* Możesz pozwolić użytkownikom na automatyczne logowanie do SolarWinds (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą SolarWinds Service Desk (wcześniej Samanage), potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja usługi Samanage z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* SolarWinds obsługuje logowanie jednokrotne w usłudze **SP**

## <a name="adding-solarwinds-from-the-gallery"></a>Dodawanie SolarWinds z galerii

Aby skonfigurować integrację programu SolarWinds z usługą Azure AD, musisz dodać SolarWinds z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać SolarWinds z galerii, wykonaj następujące czynności:**

1. W **[Azure Portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory** ikona.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **SolarWinds**, wybierz pozycję **SolarWinds** from panel wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

     ![SolarWinds na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą SolarWinds na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w SolarWinds.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi SolarWinds, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. Skonfiguruj Logowanie jednokrotne w **[usłudze SolarWinds Service](#configure-solarwinds-single-sign-on)** , aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego usługi SolarWinds Service](#create-solarwinds-test-user)** , aby uzyskać odpowiednik Britta Simon w SolarWinds Service Desk, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą SolarWinds, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **SolarWinds** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Domena i adresy URL usługi Samanage — informacje o logowaniu jednokrotnym](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<Company Name>.samanage.com/saml_login/<Company Name>`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<Company Name>.samanage.com`

    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości, używając rzeczywistego identyfikatora i adresu URL logowania, co zostało opisane w dalszej części tego samouczka. Aby uzyskać więcej informacji, skontaktuj się z [zespołem pomocy technicznej klienta usługi Samanage](https://www.samanage.com/support). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie SolarWinds** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

<a name="configure-solarwinds-single-sign-on"></a>

### <a name="configure-solarwinds-service-desk-single-sign-on"></a>Skonfiguruj pojedynczy Sign-On usługi SolarWinds Service Desk

1. W innym oknie przeglądarki sieci Web Zaloguj się do firmowej witryny SolarWinds jako administrator.

2. Kliknij pozycję **Dashboard** (Pulpit nawigacyjny) i wybierz pozycję **Setup** (Konfigurowanie) w lewym okienku nawigacji.
   
    ![Pulpit nawigacyjny](./media/samanage-tutorial/tutorial_samanage_001.png "Pulpit nawigacyjny")

3. Kliknij pozycję **Single Sign-On** (Logowanie jednokrotne).
   
    ![Logowanie jednokrotne](./media/samanage-tutorial/tutorial_samanage_002.png "Logowanie jednokrotne")

4. Przejdź do sekcji **Login using SAML** (Logowanie za pośrednictwem protokołu SAML) i wykonaj następujące kroki:
   
    ![Logowanie przy użyciu protokołu SAML](./media/samanage-tutorial/tutorial_samanage_003.png "Logowanie przy użyciu protokołu SAML")
 
    a. Kliknij pozycję **Enable Single Sign-On with SAML** (Włącz logowanie jednokrotne z użyciem protokołu SAML).  
 
    b. W polu tekstowym **Identity Provider URL** (Adres URL dostawcy tożsamości) wklej wartość pola **Identyfikator usługi Azure AD** skopiowaną z witryny Azure Portal.    
 
    c. Upewnij się, że wartość pola **Login URL** (Adres URL logowania) odpowiada **adresowi URL logowania** z sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.
 
    d. W polu tekstowym **Logout URL** (Adres URL wylogowywania) wprowadź wartość **adresu URL wylogowywania** skopiowaną z witryny Azure Portal.
 
    e. W polu tekstowym **SAML Issuer** (Wystawca SAML) wpisz identyfikator URI aplikacji ustawiony u dostawcy tożsamości.
 
    f. Otwórz w Notatniku certyfikat zakodowany w formacie Base-64 pobrany z witryny Azure Portal, skopiuj zawartość do Schowka, a następnie wklej ją w polu tekstowym **Paste your Identity Provider x.509 Certificate below** (Wklej poniżej swój certyfikat x.509 dostawcy tożsamości).
 
    przykład Kliknij pozycję **Utwórz użytkowników, jeśli nie istnieją w SolarWinds**.
 
    h. Kliknij przycisk **Update** (Aktualizuj).

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

W tej sekcji Britta Simon do korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi SolarWinds.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **SolarWinds**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **SolarWinds**.

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-solarwinds-test-user"></a>Utwórz użytkownika testowego SolarWinds

Aby umożliwić użytkownikom usługi Azure AD logowanie się w usłudze SolarWinds, muszą one być obsługiwane w usłudze SolarWinds.  
W przypadku SolarWinds, Inicjowanie obsługi administracyjnej jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do firmowej witryny SolarWinds jako administrator.

2. Kliknij pozycję **Dashboard** (Pulpit nawigacyjny) i wybierz pozycję **Setup** (Konfigurowanie) w lewym okienku nawigacji.
   
    ![Instalacja](./media/samanage-tutorial/tutorial_samanage_001.png "Konfigurowanie")

3. Kliknij kartę **Users** (Użytkownicy).
   
    ![Użytkownicy](./media/samanage-tutorial/tutorial_samanage_006.png "Użytkownicy")

4. Kliknij pozycję **New User** (Nowy użytkownik).
   
    ![Nowy użytkownik](./media/samanage-tutorial/tutorial_samanage_007.png "Nowy użytkownik")

5. W polach **Name** (Nazwa) i **Email Address** (Adres e-mail) wpisz nazwę i adres e-mail konta usługi Azure Active Directory, które chcesz aprowizować, a następnie kliknij przycisk **Create User** (Utwórz użytkownika).
   
    ![Utwórz użytkownika](./media/samanage-tutorial/tutorial_samanage_008.png "Utwórz użytkownika")
   
   >[!NOTE]
   >Właściciel konta usługi Azure Active Directory otrzyma wiadomość e-mail z linkiem, którego użyje w celu potwierdzenia konta, zanim stanie się ono aktywne. Do aprowizacji Azure Active Directory kontami użytkowników można używać innych narzędzi do tworzenia kont użytkowników lub interfejsów API udostępnionych przez program SolarWinds.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka SolarWinds w panelu dostępu należy automatycznie zalogować się do SolarWinds, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

