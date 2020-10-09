---
title: 'Samouczek: integracja Azure Active Directory z programem rozwiązania Zscaler beta | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i usługą Zscaler Beta.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/24/2019
ms.author: jeedes
ms.openlocfilehash: f342371ec065c4fb60c492c4354c6f8c717c9bb8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88546019"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-beta"></a>Samouczek: integracja Azure Active Directory z programem rozwiązania Zscaler beta

Z tego samouczka dowiesz się, jak zintegrować usługę Zscaler Beta z usługą Azure Active Directory (Azure AD).
W przypadku integracji rozwiązania Zscaler beta z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do rozwiązania Zscaler beta.
* Zezwól użytkownikom na automatyczne logowanie do rozwiązania Zscaler beta przy użyciu kont usługi Azure AD. Ta kontrola dostępu jest nazywana logowaniem jednokrotnym (SSO).
* Zarządzaj kontami w jednej centralnej lokalizacji przy użyciu Azure Portal.

Aby uzyskać więcej informacji na temat integracji aplikacji SaaS (Software as a Service) z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji usługi Azure AD z usługą Zscaler Beta są potrzebne następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja rozwiązania Zscaler beta, która korzysta z rejestracji jednokrotnej.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Rozwiązania Zscaler beta obsługuje logowanie jednokrotne zainicjowane przez usługę SP.
* Rozwiązania Zscaler beta obsługuje Inicjowanie obsługi użytkowników just in Time.

## <a name="add-zscaler-beta-from-the-azure-marketplace"></a>Dodawanie rozwiązania Zscaler beta z witryny Azure Marketplace

Aby skonfigurować integrację programu rozwiązania Zscaler beta z usługą Azure AD, Dodaj rozwiązania Zscaler beta z witryny Azure Marketplace do listy zarządzanych aplikacji SaaS.

Aby dodać rozwiązania Zscaler beta z portalu Azure Marketplace, wykonaj następujące kroki.

1. W [Azure Portal](https://portal.azure.com)w okienku nawigacji po lewej stronie wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **rozwiązania Zscaler beta**. Wybierz pozycję **rozwiązania Zscaler beta** z panelu wyników, a następnie wybierz pozycję **Dodaj**.

     ![Usługa Zscaler Beta na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą programu rozwiązania Zscaler beta w oparciu o test użytkownika Britta Simon.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w programie rozwiązania Zscaler beta.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą programu rozwiązania Zscaler beta, wykonaj następujące bloki konstrukcyjne:

- [Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on) — aby umożliwić użytkownikom korzystanie z tej funkcji.
- Skonfiguruj Logowanie jednokrotne w [wersji beta rozwiązania Zscaler](#configure-zscaler-beta-single-sign-on) , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
- [Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user) — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą użytkownika Britta Simon.
- [Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user) — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
- [Utwórz użytkownika testowego rozwiązania Zscaler beta](#create-a-zscaler-beta-test-user) , aby dysponować odpowiednikiem Britta Simon w rozwiązania Zscaler beta, który jest połączony z reprezentacją usługi Azure AD.
- [Testowanie logowania jednokrotnego](#test-single-sign-on) — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą programu rozwiązania Zscaler beta, wykonaj następujące kroki.

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji usługi **Zscaler Beta** wybierz pozycję **Logowanie jednokrotne**.

    ![Skonfiguruj link logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** wybierz pozycję **Edytuj** , aby otworzyć okno dialogowe **podstawowe ustawienia SAML** .

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja języka SAML** wykonaj następujące czynności:

    ![Informacje o rejestracji jednokrotnej w programie rozwiązania Zscaler beta i adresach URL](common/sp-intiated.png)

    - W polu **adres URL logowania** wprowadź adres URL używany przez użytkowników do logowania się do aplikacji rozwiązania Zscaler beta.

    > [!NOTE]
    > Wartość nie jest rzeczywista. Zaktualizuj wartość za pomocą rzeczywistej wartości adresu URL logowania. Aby uzyskać wartość, skontaktuj się z [zespołem pomocy technicznej rozwiązania Zscaler beta Client](https://www.zscaler.com/company/contact).

5. Aplikacja rozwiązania Zscaler beta oczekuje potwierdzeń SAML w określonym formacie. Należy dodać niestandardowe mapowania atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Wybierz pozycję **Edytuj** , aby otworzyć okno dialogowe **atrybuty użytkownika** .

    ![Okno dialogowe Atrybuty użytkownika](common/edit-attribute.png)

6. Aplikacja rozwiązania Zscaler beta oczekuje, że kilka atrybutów jest przekazanie z powrotem w odpowiedzi SAML. W sekcji **oświadczenia użytkownika** w oknie dialogowym **atrybuty użytkownika** wykonaj następujące kroki, aby dodać atrybut tokenu SAML, jak pokazano w poniższej tabeli.
    
    | Nazwa | Atrybut źródłowy | 
    | ---------------| --------------- |
    | memberOf  | user.assignedroles |

    a. Wybierz pozycję **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![Okno dialogowe oświadczenia użytkownika](common/new-save-attribute.png)

    ![Okno dialogowe Zarządzanie oświadczeniami użytkownika](common/new-attribute-details.png)

    b. W polu tekstowym **Nazwa** podaj nazwę atrybutu pokazanego dla tego wiersza.

    c. Pozostaw puste pole **obszar nazw** .

    d. W obszarze **Źródło**wybierz pozycję **atrybut**.

    e. Na liście **Atrybut źródłowy** podaj wartość atrybutu pokazanego dla tego wiersza.

    f. Wybierz przycisk **OK**.

    przykład Wybierz pozycję **Zapisz**.

    > [!NOTE]
    > Aby dowiedzieć się, jak skonfigurować role w usłudze Azure AD, zobacz [Konfigurowanie roszczeń ról](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management).

7. Na stronie **Konfigurowanie pojedynczego Sign-On za pomocą języka SAML** w sekcji **certyfikat podpisywania SAML** wybierz pozycję **Pobierz** , aby pobrać **certyfikat (base64)**. Zapisz go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

8. W sekcji **Konfigurowanie programu rozwiązania Zscaler beta** Skopiuj adresy URL, które są potrzebne do spełnienia wymagań:

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    - Adres URL logowania
    - Identyfikator usługi Azure AD
    - Adres URL wylogowywania

### <a name="configure-zscaler-beta-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w wersji beta rozwiązania Zscaler

1. Aby zautomatyzować konfigurację w programie rozwiązania Zscaler beta, zainstaluj **Moje aplikacje bezpieczne logowanie do przeglądarki** , wybierając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki wybierz pozycję Skonfiguruj program **rozwiązania Zscaler beta** kieruje do aplikacji rozwiązania Zscaler w wersji beta. W tym miejscu podaj poświadczenia administratora, aby zalogować się do programu rozwiązania Zscaler beta. Rozszerzenie przeglądarki automatycznie konfiguruje aplikację dla Ciebie i automatyzuje kroki od 3 do 6.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

3. Aby ręcznie skonfigurować rozwiązania Zscaler beta, Otwórz nowe okno przeglądarki sieci Web. Zaloguj się do witryny firmowej rozwiązania Zscaler beta jako administrator i wykonaj następujące kroki.

4. Przejdź do pozycji **Administracja**  >  **Authentication**  >  **Ustawienia uwierzytelniania**uwierzytelniania i wykonaj następujące kroki.
   
    ![Administracja](./media/zscaler-beta-tutorial/ic800206.png "Administracja")

    a. W obszarze **Typ uwierzytelniania**wybierz pozycję **SAML**.

    b. Wybierz pozycję **Konfiguruj SAML**.

5. W oknie **Edycja języka SAML** wykonaj następujące kroki: 
            
    ![Zarządzanie użytkownikami & uwierzytelnianie](./media/zscaler-beta-tutorial/ic800208.png "Zarządzanie użytkownikami & uwierzytelnianie")
    
    a. W polu **adres URL portalu SAML** wklej **adres URL logowania** skopiowany z Azure Portal.

    b. W polu **atrybut nazwy logowania** wprowadź **NameID**.

    c. W polu **publiczny certyfikat SSL** wybierz pozycję **Przekaż** , aby przekazać certyfikat podpisywania SAML platformy Azure pobrany z Azure Portal.

    d. Przełącz **opcję Włącz funkcję autoaprowizacji SAML**.

    e. W polu **atrybut Nazwa wyświetlana użytkownika** wpisz **DisplayName** , jeśli chcesz włączyć autoudostępnianie SAML dla atrybutów DisplayName.

    f. W polu **atrybut nazwy grupy** wprowadź wartość **memberOf** , jeśli chcesz włączyć funkcję autozastrzegania SAML dla atrybutów memberOf.

    przykład W polu **atrybut nazwy działu** wprowadź **dział** , jeśli chcesz włączyć funkcję autoaprowizacji SAML dla atrybutów działu.

    h. Wybierz pozycję **Zapisz**.

6. Na stronie **Konfigurowanie uwierzytelniania użytkownika** wykonaj następujące czynności:

    ![Menu aktywacja i przycisk Aktywuj](./media/zscaler-beta-tutorial/ic800207.png)

    a. Umieść kursor nad menu **Aktywacja** w lewym dolnym rogu.

    b. Wybierz pozycję **Aktywuj**.

## <a name="configure-proxy-settings"></a>Konfigurowanie ustawień serwera proxy
Aby skonfigurować ustawienia serwera proxy w programie Internet Explorer, wykonaj następujące kroki.

1. Uruchom program **Internet Explorer**.

2. Wybierz **Opcje internetowe** z menu **Narzędzia** , aby otworzyć okno dialogowe **Opcje internetowe** . 
    
     ![Opcje internetowe — okno dialogowe](./media/zscaler-beta-tutorial/ic769492.png "Opcje internetowe")

3. Wybierz kartę **połączenia** . 
  
     ![Karta Połączenia](./media/zscaler-beta-tutorial/ic769493.png "Połączenia")

4. Wybierz pozycję **Ustawienia sieci LAN** , aby otworzyć okno dialogowe **Ustawienia sieci lokalnej (LAN)** .

5. W sekcji **serwer proxy** wykonaj następujące kroki: 
   
    ![Sekcja serwera proxy](./media/zscaler-beta-tutorial/ic769494.png "Serwer proxy")

    a. Zaznacz pole wyboru **Użyj serwera proxy dla sieci LAN** .

    b. W polu **adres** wprowadź **bramę. Rozwiązania Zscaler Beta.net**.

    c. W polu **port** wprowadź **80**.

    d. Zaznacz pole wyboru **Obejdź serwer proxy dla adresów lokalnych** .

    e. Wybierz **przycisk OK** , aby zamknąć okno dialogowe **Ustawienia sieci lokalnej (LAN)** .

6. Wybierz **przycisk OK** , aby zamknąć okno dialogowe **Opcje internetowe** .

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

Utwórz użytkownika testowego w Azure Portal o nazwie Britta Simon.

1. W Azure Portal w lewym okienku wybierz pozycję **Azure Active Directory**  >  **Użytkownicy**  >  **Wszyscy użytkownicy**.

    ![Użytkownicy i wszyscy użytkownicy — linki](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. W oknie dialogowym **użytkownik** wykonaj następujące kroki:

    ![Okno dialogowe użytkownika](common/user-properties.png)

    a. W polu **Nazwa** wpisz **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** podaj wartość `brittasimon@yourcompanydomain.extension`. Może to być na przykład BrittaSimon@contoso.com.

    c. Zaznacz pole wyboru **Pokaż hasło** . Zapisz wartość wyświetlaną w polu **hasło** .

    d. Wybierz przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

Włącz logowanie jednokrotne w usłudze Britta Simon, przyznając dostęp do rozwiązania Zscaler beta.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**  >  **rozwiązania Zscaler beta**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wprowadź i wybierz pozycję **rozwiązania Zscaler beta**.

    ![Link rozwiązania Zscaler beta na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link Użytkownicy i grupy](common/users-groups-blade.png)

4. Wybierz pozycję **Dodaj użytkownika**. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy**.

    ![Przycisk Dodaj użytkownika](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz z listy użytkownika, np. **Britta Simon** . Następnie wybierz **pozycję zaznacz** w dolnej części ekranu.

    ![Użytkownicy i grupy — okno dialogowe](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_users.png)

6. W oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę użytkownika na liście. Następnie wybierz **pozycję zaznacz** w dolnej części ekranu.

    ![Okno dialogowe Wybieranie roli](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_roles.png)

7. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

    ![Okno dialogowe Dodawanie przypisania](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_assign.png)

### <a name="create-a-zscaler-beta-test-user"></a>Tworzenie użytkownika testowego rozwiązania Zscaler beta

W tej sekcji użytkownik Britta Simon jest tworzony w rozwiązania Zscaler beta. Usługa Zscaler Beta obsługuje **aprowizację użytkowników typu just in time**, która jest domyślnie włączona. Nie ma niczego do wykonania w tej sekcji. Jeśli użytkownik jeszcze nie istnieje w usłudze Zscaler Beta, to zostanie utworzony po uwierzytelnieniu.

>[!Note]
>Aby ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej rozwiązania Zscaler beta](https://www.zscaler.com/company/contact).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

Przetestuj konfigurację logowania jednokrotnego usługi Azure AD za pomocą panelu dostępu.

Po wybraniu kafelka rozwiązania Zscaler beta w panelu dostępu należy automatycznie zalogować się do rozwiązania Zscaler beta, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

