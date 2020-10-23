---
title: 'Samouczek: integracja Azure Active Directory z usługą EverBridge | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i EverBridge.
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
ms.openlocfilehash: 732362ef7099e93697320d8e47180c1207e8cb32
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92453862"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>Samouczek: integracja Azure Active Directory z usługą EverBridge

W tym samouczku dowiesz się, jak zintegrować usługę EverBridge z usługą Azure Active Directory (Azure AD).
Po zintegrowaniu usługi EverBridge z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do EverBridge.
* Zezwól użytkownikom na automatyczne logowanie do EverBridge przy użyciu kont usługi Azure AD. Ta kontrola dostępu jest nazywana logowaniem jednokrotnym (SSO).
* Zarządzaj kontami w jednej centralnej lokalizacji przy użyciu Azure Portal.
Aby uzyskać więcej informacji na temat integracji aplikacji SaaS (Software as a Service) z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą EverBridge, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja EverBridge, która korzysta z rejestracji jednokrotnej.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa EverBridge obsługuje logowanie jednokrotne inicjowane przez usługę dostawcy tożsamości.

## <a name="add-everbridge-from-the-azure-marketplace"></a>Dodawanie EverBridge z witryny Azure Marketplace

Aby skonfigurować integrację programu EverBridge z usługą Azure AD, Dodaj EverBridge z witryny Azure Marketplace do listy zarządzanych aplikacji SaaS.

Aby dodać EverBridge z portalu Azure Marketplace, wykonaj następujące kroki.

1. W [Azure Portal](https://portal.azure.com)w okienku nawigacji po lewej stronie wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **EverBridge**. Wybierz pozycję **EverBridge** w panelu wyników, a następnie wybierz pozycję **Dodaj**.

     ![EverBridge na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą EverBridge w oparciu o test użytkownika Britta Simon.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w EverBridge.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi EverBridge, wykonaj następujące bloki konstrukcyjne:

- [Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on) — aby umożliwić użytkownikom korzystanie z tej funkcji.
- [Skonfiguruj EverBridge jako logowanie](#configure-everbridge-as-everbridge-manager-portal-single-sign-on) jednokrotne w programie EverBridge Manager, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
- [Skonfiguruj EverBridge jako logowanie jednokrotne w portalu elementów członkowskich,](#configure-everbridge-as-everbridge-member-portal-single-sign-on) aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
- [Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user) — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą użytkownika Britta Simon.
- [Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user) — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
- [Utwórz użytkownika testowego EverBridge](#create-an-everbridge-test-user) , aby uzyskać odpowiednik Britta Simon w EverBridge, który jest połączony z reprezentacją usługi Azure AD.
- [Testowanie logowania jednokrotnego](#test-single-sign-on) — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą EverBridge, wykonaj następujące kroki.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **EverBridge** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** wybierz pozycję **Edytuj** , aby otworzyć okno dialogowe **podstawowe ustawienia SAML** .

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

    >[!NOTE]
    >Skonfiguruj aplikację jako portal Menedżera *lub* jako portal elementu członkowskiego na Azure Portal i w portalu EverBridge.

4. Aby skonfigurować aplikację **EverBridge** jako **Portal Menedżera EverBridge**, w sekcji **podstawowe konfiguracje języka SAML** wykonaj następujące czynności:

    ![EverBridge domenę i adresy URL Logowanie jednokrotne](common/idp-intiated.png)

    a. W polu **Identyfikator** wprowadź adres URL, który następuje po wzorcu `https://sso.everbridge.net/<API_Name>`

    b. W polu **adres URL odpowiedzi** wprowadź adres URL zgodny ze wzorcem. `https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias`

    > [!NOTE]
    > To nie są rzeczywiste wartości. Zaktualizuj te wartości za pomocą wartości rzeczywiste identyfikator i adres URL odpowiedzi. Aby uzyskać te wartości, skontaktuj się z [zespołem pomocy technicznej EverBridge](mailto:support@everbridge.com). Można również odwołać się do wzorców przedstawionych w sekcji podstawowe informacje o **konfiguracji SAML** w Azure Portal.

5. Aby skonfigurować aplikację **EverBridge** jako **Portal elementu członkowskiego EverBridge**, w sekcji **podstawowe konfiguracje języka SAML** wykonaj następujące czynności:

  * Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez dostawcy tożsamości, wykonaj następujące kroki:

     ![EverBridge domenę i adresy URL logowania jednokrotnego dla trybu zainicjowane przez dostawcy tożsamości](common/idp-intiated.png)

    a. W polu **Identyfikator** wprowadź adres URL, który następuje po wzorcu `https://sso.everbridge.net/<API_Name>/<Organization_ID>`

    b. W polu **adres URL odpowiedzi** wprowadź adres URL zgodny ze wzorcem. `https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias`

   * Jeśli chcesz skonfigurować aplikację w trybie inicjowania programu SP, wybierz opcję **Ustaw dodatkowe adresy URL** i wykonaj następujące czynności:

     ![EverBridge domenę i adresy URL logowania jednokrotnego w trybie zainicjowanym przez program SP](common/both-signonurl.png)

     a. W polu **adres URL logowania** wprowadź adres URL zgodny ze wzorcem `https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true`

     > [!NOTE]
     > To nie są rzeczywiste wartości. Zaktualizuj te wartości przy użyciu wartości rzeczywiste identyfikator, adres URL odpowiedzi i adres URL logowania. Aby uzyskać te wartości, skontaktuj się z [zespołem pomocy technicznej EverBridge](mailto:support@everbridge.com). Można również odwołać się do wzorców przedstawionych w sekcji podstawowe informacje o **konfiguracji SAML** w Azure Portal.

6. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** wybierz pozycję **Pobierz** , aby pobrać **plik XML metadanych Federacji**. Zapisz go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

7. W sekcji **Konfiguracja EverBridge** Skopiuj adresy URL, które są potrzebne do spełnienia wymagań:

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    - Adres URL logowania
    - Identyfikator usługi Azure AD
    - Adres URL wylogowywania

### <a name="configure-everbridge-as-everbridge-manager-portal-single-sign-on"></a>Konfigurowanie EverBridge jako rejestracji jednokrotnej w portalu EverBridge Manager

Aby skonfigurować Logowanie jednokrotne w usłudze **EverBridge** jako aplikację **portalu EverBridge Manager** , wykonaj następujące kroki.
 
1. W innym oknie przeglądarki sieci Web Zaloguj się do EverBridge jako administrator.

1. W menu u góry wybierz kartę **Ustawienia** . W obszarze **zabezpieczenia**wybierz pozycję **Logowanie jednokrotne**.
   
     ![Konfigurowanie logowania jednokrotnego](./media/everbridge-tutorial/tutorial_everbridge_002.png)
   
     a. W polu **Nazwa** wprowadź nazwę dostawcy identyfikatora. Przykładem jest nazwa firmy.
   
     b. W polu **nazwa interfejsu API** wprowadź nazwę interfejsu API.
   
     c. Wybierz pozycję **Wybierz plik** , aby przekazać plik metadanych pobrany z Azure Portal.
   
     d. W polu **Lokalizacja tożsamości SAML**zaznacz pozycję **tożsamość w elemencie NameIdentifier instrukcji subject**.
   
     e. W polu **adres URL logowania dostawcy tożsamości** wklej wartość **adresu URL logowania** skopiowaną z Azure Portal.
   
     f. W przypadku **powiązania żądania zainicjowane przez dostawcę usług**wybierz opcję **przekierowywanie http**.

     przykład Wybierz pozycję **Zapisz**.

### <a name="configure-everbridge-as-everbridge-member-portal-single-sign-on"></a>Konfigurowanie EverBridge jako rejestracji jednokrotnej w portalu członków EverBridge

Aby skonfigurować Logowanie jednokrotne na **EverBridge** jako **Portal elementu członkowskiego EverBridge**, Wyślij pobrany **XML metadanych Federacji** do [zespołu pomocy technicznej EverBridge](mailto:support@everbridge.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

Aby utworzyć test User Britta Simon w Azure Portal, wykonaj następujące kroki.

1. W Azure Portal w lewym okienku wybierz pozycję **Azure Active Directory**  >  **Użytkownicy**  >  **Wszyscy użytkownicy**.

    ![Użytkownicy i wszyscy użytkownicy — linki](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. W oknie dialogowym **Użytkownik** wykonaj następujące kroki.

    ![Okno dialogowe użytkownika](common/user-properties.png)

    a. W polu **Nazwa** wpisz **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** podaj wartość `brittasimon@yourcompanydomain.extension`. Może to być na przykład BrittaSimon@contoso.com.

    c. Zaznacz pole wyboru **Pokaż hasło** . Zapisz wartość wyświetlaną w polu **hasło** .

    d. Wybierz pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

Włącz logowanie jednokrotne w usłudze Britta Simon, przyznając dostęp do usługi EverBridge.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**  > **EverBridge**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **EverBridge**.

    ![Link EverBridge na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link Użytkownicy i grupy](common/users-groups-blade.png)

4. Wybierz pozycję **Dodaj użytkownika**. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy**.

    ![Okno dialogowe Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **Britta Simon** na liście Użytkownicy. Użyj pozycji **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz jakiejkolwiek wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybieranie roli** wybierz odpowiednią rolę dla użytkownika z listy. Użyj pozycji **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

### <a name="create-an-everbridge-test-user"></a>Tworzenie użytkownika testowego EverBridge

W tej sekcji utworzysz test User Britta Simon w programie EverBridge. Aby dodać użytkowników na platformie EverBridge, należy skontaktować się z [zespołem pomocy technicznej EverBridge](mailto:support@everbridge.com). Przed użyciem logowania jednokrotnego należy utworzyć i aktywować użytkowników w usłudze EverBridge. 

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

Przetestuj konfigurację logowania jednokrotnego usługi Azure AD za pomocą panelu dostępu.

Po wybraniu kafelka EverBridge w panelu dostępu należy automatycznie zalogować się do konta EverBridge, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)
- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)