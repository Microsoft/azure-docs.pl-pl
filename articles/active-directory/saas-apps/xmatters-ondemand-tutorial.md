---
title: 'Samouczek: integracja Azure Active Directory z usługą xMatters OnDemand | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i xMatters OnDemand.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: 648bb5eb5daab20248e16f2d1606f61a3fc52d17
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92520404"
---
# <a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>Samouczek: integracja Azure Active Directory z usługą xMatters OnDemand

W tym samouczku dowiesz się, jak zintegrować usługę xMatters OnDemand z usługą Azure Active Directory (Azure AD).
Integracja usługi xMatters OnDemand z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować w usłudze Azure AD, kto ma dostęp do xMatters OnDemand.
* Możesz umożliwić użytkownikom automatyczne logowanie do xMatters OnDemand (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą xMatters OnDemand, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* subskrypcja z włączonym logowaniem jednokrotnym w usłudze xMatters OnDemand

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa xMatters OnDemand obsługuje **dostawcy tożsamości** zainicjowane przez usługę SSO

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>Dodawanie xMatters OnDemand z galerii

Aby skonfigurować integrację usługi xMatters OnDemand w usłudze Azure AD, musisz dodać OnDemand xMatters z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać xMatters OnDemand z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **XMatters OnDemand**, wybierz pozycję **xMatters OnDemand** from a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

     ![xMatters OnDemand na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą xMatters OnDemand na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w programie xMatters OnDemand.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi xMatters OnDemand, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie](#configure-xmatters-ondemand-single-sign-on)** jednokrotne w usłudze xMatters OnDemand — aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego XMatters OnDemand](#create-xmatters-ondemand-test-user)** , aby uzyskać odpowiednik Simon Britta w xMatters OnDemand, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą xMatters OnDemand, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **xMatters OnDemand** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** wykonaj następujące kroki:

    ![xMatters OnDemand domeny i adresy URL Logowanie jednokrotne](common/idp-intiated.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: 

    - `https://<companyname>.au1.xmatters.com.au/`
    - `https://<companyname>.cs1.xmatters.com/`
    - `https://<companyname>.xmatters.com/`
    - `https://www.xmatters.com`
    - `https://<companyname>.xmatters.com.au/`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: 

    - `https://<companyname>.au1.xmatters.com.au`
    - `https://<companyname>.xmatters.com/sp/<instancename>`
    - `https://<companyname>.cs1.xmatters.com/sp/<instancename>`
    - `https://<companyname>.au1.xmatters.com.au/<instancename>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Skontaktuj się z [zespołem obsługi klienta XMatters OnDemand](https://www.xmatters.com/company/contact-us/) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

    > [!IMPORTANT]
    > Musisz przesłać dalej certyfikat do [zespołu pomocy technicznej XMatters OnDemand](https://www.xmatters.com/company/contact-us/). Aby można było sfinalizować konfigurację logowania jednokrotnego, certyfikat musi zostać przekazany przez zespół pomocy technicznej xMatters.

6. W sekcji **Konfigurowanie XMatters OnDemand** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-xmatters-ondemand-single-sign-on"></a>Konfigurowanie xMatters OnDemand pojedynczego Sign-On

1. W innym oknie przeglądarki sieci Web Zaloguj się do witryny firmy XMatters OnDemand jako administrator.

2. Na pasku narzędzi u góry kliknij pozycję **administrator**, a następnie kliknij pozycję **szczegóły firmy** na pasku nawigacyjnym po lewej stronie.

    ![Administracja](./media/xmatters-ondemand-tutorial/IC776795.png "Administracja")

3. Na stronie **SAML Configuration** (Konfiguracja SAML) wykonaj następujące kroki:

    ![Konfiguracja SAML](./media/xmatters-ondemand-tutorial/IC776796.png "Konfiguracja SAML")

    a. Wybierz pozycję **Enable SAML** (Włącz SAML).

    b. W polu tekstowym **Identyfikator dostawcy tożsamości** wklej wartość **identyfikatora usługi Azure AD** skopiowaną z Azure Portal.

    c. W polu tekstowym **adres URL** logowania jednokrotnego wklej wartość **adresu URL logowania** , która została skopiowana z Azure Portal.

    d. W polu tekstowym **adres URL wylogowania jednokrotnego** wklej **adres URL wylogowania**, który został skopiowany z Azure Portal.

    e. Na stronie Szczegóły firmy u góry kliknij przycisk **Zapisz zmiany**.

    ![Szczegóły firmy](./media/xmatters-ondemand-tutorial/IC776797.png "Szczegóły firmy")

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

W tej sekcji Britta Simon do korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do xMatters OnDemand.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **xMatters OnDemand**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **XMatters OnDemand**.

    ![Link xMatters OnDemand na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-xmatters-ondemand-test-user"></a>Utwórz użytkownika testowego xMatters OnDemand

Celem tej sekcji jest utworzenie użytkownika o nazwie Britta Simon w xMatters OnDemand.

**Jeśli potrzebujesz utworzyć użytkownika ręcznie, wykonaj następujące czynności:**

1. Zaloguj się do dzierżawy **OnDemand XMatters** .

2. Kliknij kartę **Użytkownicy** , a następnie kliknij przycisk **Dodaj użytkownika**.

    ![Użytkownicy](./media/xmatters-ondemand-tutorial/IC781048.png "Użytkownicy")

3. W sekcji **Dodawanie użytkownika** wykonaj następujące czynności:

    ![Dodawanie użytkownika](./media/xmatters-ondemand-tutorial/IC781049.png "Dodawanie użytkownika")

    a. Wybierz pozycję **Active** (Aktywne).

    b. W polu tekstowym **Identyfikator użytkownika** wpisz identyfikator użytkownika, na przykład Brittasimon@contoso.com .

    c. W polu tekstowym **First Name** (Imię) wpisz imię użytkownika, takie jak Britta.

    d. W polu tekstowym **Last Name** (Nazwisko) wpisz nazwisko użytkownika, takie jak Simon.

    e. W polu tekstowym **lokacja** Wprowadź prawidłową witrynę prawidłowego konta usługi Azure AD, dla którego chcesz zainicjować obsługę administracyjną.

    f. Kliknij pozycję **Zapisz**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka xMatters OnDemand w panelu dostępu należy automatycznie zalogować się do OnDemand xMatters, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)