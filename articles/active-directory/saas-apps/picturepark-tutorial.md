---
title: 'Samouczek: integracja Azure Active Directory z usługą Picturepark | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Picturepark.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: 8a00cf11edfea2e732a18a392d465525b38ea45f
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92520859"
---
# <a name="tutorial-azure-active-directory-integration-with-picturepark"></a>Samouczek: integracja Azure Active Directory z usługą Picturepark

W tym samouczku dowiesz się, jak zintegrować usługę Picturepark z usługą Azure Active Directory (Azure AD).
Integracja Picturepark z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do usługi Picturepark.
* Możesz pozwolić użytkownikom na automatyczne logowanie do Picturepark (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą Picturepark, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Subskrypcja z włączonym logowaniem jednokrotnym w Picturepark

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Picturepark obsługuje logowanie jednokrotne w usłudze **SP**

## <a name="adding-picturepark-from-the-gallery"></a>Dodawanie Picturepark z galerii

Aby skonfigurować integrację programu Picturepark z usługą Azure AD, musisz dodać Picturepark z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Picturepark z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Picturepark**, wybierz pozycję **Picturepark** from panel wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

     ![Picturepark na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą Picturepark na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Picturepark.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi Picturepark, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie](#configure-picturepark-single-sign-on)** jednokrotne w usłudze Picturepark, aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego Picturepark](#create-picturepark-test-user)** , aby uzyskać odpowiednik Britta Simon w Picturepark, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą Picturepark, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Picturepark** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Picturepark domenę i adresy URL Logowanie jednokrotne](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<companyname>.picturepark.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: 

    ```http
        https://<companyname>.current-picturepark.com
        https://<companyname>.picturepark.com
        https://<companyname>.next-picturepark.com
    ```

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z [zespołem obsługi klienta Picturepark](https://picturepark.com/company/picturepark-customer-support) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. W sekcji **Certyfikat podpisywania SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Certyfikat podpisywania SAML**.

    ![Edytowanie certyfikatu podpisywania SAML](common/edit-certificate.png)

6. W sekcji **certyfikat podpisywania SAML** Skopiuj **odcisk palca** i Zapisz go na komputerze.

    ![Kopiowanie wartości Odcisk palca](common/copy-thumbprint.png)

7. W sekcji **Konfigurowanie Picturepark** skopiuj odpowiednie adresy URL zgodnie z wymaganiami. W polu **adres URL logowania**Użyj wartości z następującym wzorcem: `https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > _my_directory_id_ to identyfikator dzierżawy subskrypcji usługi Azure AD.

    ![Kopiowanie adresów URL konfiguracji](./media/picturepark-tutorial/configurls.png)

    a. Identyfikator usługi Azure AD

    b. Adres URL wylogowywania

### <a name="configure-picturepark-single-sign-on"></a>Konfigurowanie pojedynczego Sign-On Picturepark

1. W innym oknie przeglądarki sieci Web Zaloguj się do firmowej witryny Picturepark jako administrator.

2. Na pasku narzędzi u góry kliknij pozycję **Narzędzia administracyjne**, a następnie kliknij pozycję **Konsola zarządzania**.
   
    ![Konsola zarządzania](./media/picturepark-tutorial/ic795062.png "Konsola zarządzania")

3. Kliknij pozycję **uwierzytelnianie**, a następnie kliknij pozycję **dostawcy tożsamości**.
   
    ![Authentication](./media/picturepark-tutorial/ic795063.png "Authentication")

4. W sekcji **Konfiguracja dostawcy tożsamości** wykonaj następujące czynności:
   
    ![Konfiguracja dostawcy tożsamości](./media/picturepark-tutorial/ic795064.png "Konfiguracja dostawcy tożsamości")
   
    a. Kliknij pozycję **Dodaj**.
  
    b. Wpisz nazwę konfiguracji.
   
    c. Wybierz pozycję **Ustaw jako domyślną**.
   
    d. W polu tekstowym **Identyfikator URI wystawcy** wklej wartość **adresu URL logowania** , który został skopiowany z Azure Portal.
   
    e. W polu tekstowym **nadruk wystawcy zaufanych wystawców** wklej wartość **odcisku palca** skopiowanego z sekcji **certyfikat podpisywania SAML** . 

5. Kliknij pozycję **JoinDefaultUsersGroup**.

6. Aby ustawić atrybut **EmailAddress** w polu tekstowym **Claim** , wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` i kliknij przycisk **Zapisz**.

      ![Konfiguracja](./media/picturepark-tutorial/ic795065.png "Konfiguracja")

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz `brittasimon@yourcompanydomain.extension` . Na przykład BrittaSimon@contoso.com

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji Britta Simon do korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Picturepark.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **Picturepark**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **Picturepark**.

    ![Link Picturepark na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-picturepark-test-user"></a>Utwórz użytkownika testowego Picturepark

Aby umożliwić użytkownikom usługi Azure AD logowanie się w usłudze Picturepark, muszą one być obsługiwane w Picturepark. W przypadku Picturepark, Inicjowanie obsługi administracyjnej jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do dzierżawy **Picturepark** .

1. Na pasku narzędzi u góry kliknij pozycję **Narzędzia administracyjne**, a następnie kliknij pozycję **Użytkownicy**.
   
    ![Użytkownicy](./media/picturepark-tutorial/ic795067.png "Użytkownicy")

1. Na karcie **Przegląd użytkowników** kliknij pozycję **Nowy**.
   
    ![Zarządzanie użytkownikami](./media/picturepark-tutorial/ic795068.png "Zarządzanie użytkownikami")

1. W oknie dialogowym **Tworzenie użytkownika** wykonaj następujące kroki prawidłowego użytkownika Azure Active Directory, który chcesz udostępnić:
   
    ![Utwórz użytkownika](./media/picturepark-tutorial/ic795069.png "Utwórz użytkownika")
   
    a. W polu tekstowym **adres e-mail** wpisz **adres e-mail** użytkownika `BrittaSimon@contoso.com` .  
   
    b. W polach tekstowych **hasło** i **Potwierdź hasło** wpisz **hasło** BrittaSimon. 
   
    c. W polu tekstowym **imię i nazwisko** , wpisz **imię** **Britta**użytkownika. 
   
    d. W polu **tekstowym nazwisko** wpisz **nazwisko** użytkownika **Simon**.
   
    e. W polu tekstowym **firma** wpisz **nazwę firmy** użytkownika. 
   
    f. W polu tekstowym **Country (kraj** ) wybierz **kraj/region** użytkownika.
  
    przykład W polu tekstowym **zip** wpisz **Kod pocztowy** miasta.
   
    h. W polu tekstowym **miasto** wpisz **nazwę miasta** użytkownika.

    i. Wybierz **Język**.
   
    j. Kliknij przycisk **Utwórz**.

>[!NOTE]
>Do aprowizacji kont użytkowników usługi Azure AD można używać innych narzędzi do tworzenia kont użytkowników Picturepark i interfejsów API udostępnionych przez usługę Picturepark.
>

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Picturepark w panelu dostępu należy automatycznie zalogować się do Picturepark, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)