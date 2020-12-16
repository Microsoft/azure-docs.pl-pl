---
title: 'Samouczek: integracja Azure Active Directory z usługą Wdesk | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Wdesk.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/02/2020
ms.author: jeedes
ms.openlocfilehash: 11539e7d6e685703810537cf69e0e7ed3ec0be4c
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97609092"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-wdesk"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą Wdesk

W tym samouczku dowiesz się, jak zintegrować usługę Wdesk z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Wdesk z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Wdesk.
* Zezwól użytkownikom na automatyczne logowanie się do usługi Wdesk przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) Wdesk.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Wdesk obsługuje usługę **SP** i **dostawcy tożsamości** zainicjowano Logowanie jednokrotne
* Po skonfigurowaniu Wdesk można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozszerzy od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

## <a name="adding-wdesk-from-the-gallery"></a>Dodawanie Wdesk z galerii

Aby skonfigurować integrację programu Wdesk z usługą Azure AD, musisz dodać Wdesk z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Wdesk** w polu wyszukiwania.
1. Wybierz pozycję **Wdesk** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą Wdesk na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Wdesk.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Wdesk, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-wdesk-sso)** w usłudze Wdesk, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego Wdesk](#create-wdesk-test-user)** , aby dysponować odpowiednikiem B. Simon w Wdesk, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą Wdesk, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Wdesk** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia podstawową konfigurację języka SAML, w której można wprowadzić identyfikator, odpowiedź U R L i wybrać pozycję Zapisz.](common/idp-intiated.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>`

5. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    ![Zrzut ekranu przedstawia ustawienie dodatkowego U R LS, gdzie można wprowadzić znak U R L.](common/metadata-upload-additional-signon.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.wdesk.com/auth/login/saml/<instancename>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Te wartości są uzyskiwane z portalu WDesk podczas konfigurowania logowania jednokrotnego.

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W sekcji **Konfigurowanie Wdesk** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

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

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji Britta Simon do korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Wdesk.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **Wdesk**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **Wdesk**.

    ![Link Wdesk na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

## <a name="configure-wdesk-sso"></a>Konfigurowanie logowania jednokrotnego Wdesk

1. W innym oknie przeglądarki sieci Web Zaloguj się, aby Wdesk jako administrator zabezpieczeń.

1. W lewym dolnym rogu kliknij pozycję **administrator** i wybierz pozycję **administrator konta**:
 
    ![Zrzut ekranu przedstawia administratora konta wybranego z menu administratora.](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

1. W obszarze Administrator Wdesk przejdź do obszarze **zabezpieczenia**, a następnie pozycję **SAML**  >  **SAML Settings**:

    ![Zrzut ekranu przedstawia ustawienia protokołu SAML wybrane z karty SAML.](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig2.png)

1. W obszarze **ustawienia identyfikatora użytkownika SAML** Sprawdź, czy **Identyfikator użytkownika SAML to Wdesk username**.

    ![Zrzut ekranu przedstawia ustawienia użytkownika protokołu SAML I D, w których można wybrać użytkownika SAML, I D jest nazwą użytkownika W usłudze.](./media/wdesk-tutorial/wdesk-username.png)

4. W obszarze **Ustawienia ogólne** zaznacz pole wyboru **Włącz logowanie jednokrotne SAML**:

    ![Zrzut ekranu przedstawia Edytowanie ustawień SAML, w których można wybrać opcję Włącz logowanie jednokrotne SAML.](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig3.png)

5. W obszarze **szczegóły dostawcy usług** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia szczegóły dostawcy usług, w którym można wprowadzić podane wartości.](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig4.png)

    1. Skopiuj **adres URL logowania** i wklej go w polu tekstowym **adres url logowania** na Azure Portal.

    1. Skopiuj **adres URL metadanych** i wklej go w polu tekstowym **identyfikatora** na Azure Portal.

    1. Skopiuj **adres URL klienta** i wklej go w polu tekstowym **adres url odpowiedzi** na Azure Portal.

    1. Kliknij przycisk **Zapisz** na Azure Portal, aby zapisać zmiany.      

1. Kliknij pozycję **Konfiguruj ustawienia dostawcy tożsamości** , aby otworzyć okno dialogowe **Edytowanie ustawień dostawcy tożsamości** . Kliknij pozycję **Wybierz plik** , aby zlokalizować plik **Metadata.xml** zapisany z Azure Portal, a następnie Przekaż go.
    
    ![Zrzut ekranu przedstawia ustawienia w obszarze Edycja I d P, w których można przekazać metadane.](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig5.png)
  
1. Kliknij przycisk **Zapisz zmiany**.

    ![Zrzut ekranu przedstawia przycisk Zapisz zmiany.](./media/wdesk-tutorial/tutorial_wdesk_ssoconfigsavebutton.png)

### <a name="create-wdesk-test-user"></a>Utwórz użytkownika testowego Wdesk

Aby umożliwić użytkownikom usługi Azure AD logowanie się w usłudze Wdesk, muszą one być obsługiwane w usłudze Wdesk. W Wdesk, Inicjowanie obsługi jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do Wdesk jako administrator zabezpieczeń.

2. Przejdź do   >  **administratora konta** administratora.

     ![Zrzut ekranu przedstawia administratora konta wybranego z menu administratora.](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. Kliknij pozycję **Członkowie** w obszarze **osoby**.

4. Teraz kliknij przycisk **Dodaj członka** , aby otworzyć okno dialogowe **Dodawanie elementu członkowskiego** . 
   
    ![Zrzut ekranu przedstawia kartę Członkowie, w której można wybrać opcję Dodaj członka.](./media/wdesk-tutorial/createuser1.png)  

5. W polu tekstowym **użytkownik** wprowadź nazwę użytkownika, jak b.simon@contoso.com i kliknij przycisk **Kontynuuj** .

    ![Zrzut ekranu przedstawia okno dialogowe Dodawanie elementu członkowskiego, w którym można wprowadzić użytkownika.](./media/wdesk-tutorial/createuser3.png)

6.  Wprowadź szczegóły, jak pokazano poniżej:
  
    ![Zrzut ekranu przedstawia okno dialogowe Dodawanie elementu członkowskiego, w którym można dodać podstawowe informacje dla użytkownika.](./media/wdesk-tutorial/createuser4.png)
 
    a. W polu tekstowym **adres e-mail** wprowadź adres e-mail użytkownika, np b.simon@contoso.com .

    b. W polu tekstowym **imię i nazwisko** , wprowadź imię użytkownika, np. **B**.

    c. W polu tekstowym **nazwisko** Wprowadź nazwisko użytkownika, np. **Simon**.

7. Kliknij przycisk **Zapisz element członkowski** .  

    ![Zrzut ekranu przedstawia przycisk Wyślij wiadomość e-mail powitalnego za pomocą przycisku Zapisz członka.](./media/wdesk-tutorial/createuser5.png)

### <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Wdesk w panelu dostępu należy automatycznie zalogować się do Wdesk, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)