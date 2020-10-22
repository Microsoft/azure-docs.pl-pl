---
title: 'Samouczek: integracja Azure Active Directory z kontrolką Workspot | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne dla Azure Active Directory i formantu Workspot.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: jeedes
ms.openlocfilehash: bfbecc71638e6feaaf29809f09dda752dd29b2ae
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "88526589"
---
# <a name="tutorial-azure-active-directory-integration-with-workspot-control"></a>Samouczek: integracja Azure Active Directory z kontrolką Workspot

W tym samouczku dowiesz się, jak zintegrować formant Workspot z usługą Azure Active Directory (Azure AD). Po zintegrowaniu z usługą Azure AD kontroli Workspot można:

* Użyj usługi Azure AD, aby kontrolować, kto ma dostęp do kontrolki Workspot.
* Zezwól użytkownikom na automatyczne logowanie do kontrolki Workspot (Logowanie jednokrotne]) przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji: Azure Portal.

Aby uzyskać więcej informacji na temat integracji aplikacji SaaS z usługą Azure AD, zobacz Logowanie jednokrotne [do aplikacji w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z kontrolką Workspot, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).

* Ta subskrypcja z włączoną obsługą logowania jednokrotnego (Workspot).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

> [!Note]
> Kontrolka Workspot obsługuje logowanie jednokrotne zainicjowane przez usługę SP i dostawcy tożsamości.


## <a name="adding-workspot-control-from-the-gallery"></a>Dodawanie formantu Workspot z galerii

Aby skonfigurować integrację kontroli Workspot z usługą Azure AD, musisz dodać kontrolkę Workspot z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać kontrolkę Workspot z galerii, wykonaj następujące kroki:**

1. W lewym okienku [Azure Portal](https://portal.azure.com)wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw** i wybierz pozycję **wszystkie aplikacje**.

    ![Okienko Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Wybierz pozycję **Nowa aplikacja** w górnej części okna.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **kontrolkę Workspot**, wybierz pozycję **Workspot Control** w panelu wyniki, a następnie wybierz pozycję **Dodaj**.

     ![Okno "Dodaj z galerii"](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i testujesz Logowanie jednokrotne w usłudze Azure AD za pomocą kontrolki Workspot dla użytkownika testowego Britta Simon.
Aby logowanie jednokrotne działało, należy nawiązać połączenie między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w kontrolce Workspot.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą kontrolki Workspot, należy wykonać następujące zadania:

1. [Skonfiguruj logowanie jednokrotne w usłudze Azure AD](#configure-azure-ad-single-sign-on), aby umożliwić użytkownikom korzystanie z tej funkcji.
2. Skonfiguruj Logowanie jednokrotne w [kontrolce Workspot](#configure-workspot-control-single-sign-on) , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. [Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user) , aby przetestować Logowanie jednokrotne w usłudze Azure AD dla Britta Simon.
4. [Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user) — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. [Utwórz użytkownika testowego kontrolki Workspot](#create-a-workspot-control-test-user) , aby ustanowić odpowiednik Britta Simon w kontrolce Workspot, która jest połączona z reprezentacją usługi Azure AD.
6. [Przetestuj logowanie jednokrotne](#test-single-sign-on), aby sprawdzić działanie konfiguracji.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą kontrolki Workspot, wykonaj następujące kroki:

1. Na stronie integracja aplikacji **Workspot Control** w [Azure Portal](https://portal.azure.com/)wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie **Wybierz metodę logowania** jednokrotnego wybierz tryb **SAML** , aby włączyć logowanie jednokrotne.

    ![Wybierz okno metody wyboru logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** wybierz ikonę **Edytuj** (ołówek), aby uzyskać dostęp do **podstawowej konfiguracji języka SAML**.

    ![Ikona edycji wyróżniona w temacie "Podstawowa konfiguracja SAML"](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przy użyciu dostawcy tożsamości, w sekcji **Podstawowa konfiguracja SAML** wykonaj następujące kroki:

    ![Informacje o rejestracji jednokrotnej w domenie i adresach URL Workspot](common/idp-intiated.png)

    1. W polu tekstowym **Identyfikator** wprowadź adres URL w następującym wzorcu:<br/>
    **_https://<<i></i> INSTANCENAME>-saml.workspot.com/saml/metadata_*_

    1. W _polu tekstowym *adres URL odpowiedzi** wprowadź adres URL w następującym wzorcu: <br/> 
    * *_https://<<i></i> InstanceName>-SAML.Workspot.com/SAML/Assertion_*_

5. Jeśli chcesz skonfigurować aplikację w trybie zainicjowanym z użyciem programu SP, wybierz pozycję _ * Ustaw dodatkowe adresy URL * *.

    ![Informacje o rejestracji jednokrotnej w domenie i adresach URL Workspot](common/metadata-upload-additional-signon.png)

    W polu tekstowym **adres URL logowania** wprowadź adres URL w następującym wzorcu:<br/>
    **_https://<<i></i> INSTANCENAME>-saml.workspot.com/_*_

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistym identyfikatorem, adresem URL odpowiedzi i adresem URL logowania. Aby uzyskać te wartości, skontaktuj się z [zespołem pomocy technicznej Workspot Control Client](mailto:support@workspot.com) . Można również odwołać się do wzorców w sekcji _*Podstawowa konfiguracja SAML** w Azure Portal.

6. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** wybierz pozycję **Pobierz** , aby pobrać **certyfikat (base64)** z dostępnych opcji zgodnie z wymaganiami. Zapisz go na komputerze.

    ![Link do pobierania certyfikatu (base64)](common/certificatebase64.png)

7. W sekcji **Konfigurowanie formantu Workspot** skopiuj odpowiednie adresy URL zgodnie z wymaganiami:

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    - **Adres URL logowania**

    - **Identyfikator usługi Azure AD**

    - **Adres URL wylogowywania**

### <a name="configure-workspot-control-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w kontrolce Workspot

1. W innym oknie przeglądarki sieci Web Zaloguj się, aby Workspot kontrolę jako administrator zabezpieczeń.

2. Na pasku narzędzi w górnej części strony wybierz pozycję **Instalator** , a następnie opcję **SAML**.

    ![Opcje konfiguracji](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_setup.png)

3. W oknie **konfiguracja SAML** wykonaj następujące kroki:
 
    ![Okno konfiguracji SAML](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_saml.png)

    1. W polu **Identyfikator jednostki** wklej **Identyfikator usługi Azure AD** , który został skopiowany z Azure Portal.

    1. W polu **adres URL usługi jednokrotnego** wklej **adres URL logowania** skopiowany z Azure Portal.

    1. W polu **adres URL usługi Wyloguj** wklej **adres URL wylogowania** skopiowany z Azure Portal.

    1. Wybierz pozycję **Aktualizuj plik** , aby przekazać do certyfikatu X. 509 certyfikat z kodowaniem base-64, który został pobrany z Azure Portal.

    1. Wybierz pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal.

1. W lewym okienku Azure Portal wybierz **Azure Active Directory**, **Użytkownicy**, a następnie **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części okna.

    ![Przycisk "nowy użytkownik"](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki:

    ![Okno właściwości użytkownika](common/user-properties.png)

    1. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    1. W polu **Nazwa użytkownika** wpisz **brittasimon@* yourcompanydomain. Extension * * *. Na przykład wprowadź ** BrittaSimon@contoso . <i></i> Model com**.

    1. Zaznacz pole wyboru **Pokaż hasło** . Następnie Zapisz wartość wyświetlaną w polu **hasło** .

    1. Wybierz pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji przyznano Britta Simon dostęp do kontrolki Workspot, aby umożliwić jej korzystanie z rejestracji jednokrotnej na platformie Azure.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, **wszystkie aplikacje**, a następnie **kontrolkę Workspot**.

    ![Okienko Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Z listy Aplikacje wybierz pozycję **formant Workspot**.

    ![Link kontrolny Workspot na liście aplikacji](common/all-applications.png)

3. Z menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Wybierz przycisk **Dodaj użytkownika** . Następnie w oknie **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Okno "Dodawanie przypisania"](common/add-assign-user.png)

5. W oknie **Użytkownicy i grupy** wybierz pozycję **Britta Simon** na liście **Użytkownicy** . Następnie kliknij pozycję **Wybierz**.

6. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, wybierz odpowiednią rolę dla użytkownika z listy w oknie **Wybieranie roli** . Następnie kliknij pozycję **Wybierz** u dołu.

7. W oknie **Dodawanie przypisania** wybierz pozycję **Przypisz**.

### <a name="create-a-workspot-control-test-user"></a>Tworzenie użytkownika testowego kontrolki Workspot

Aby umożliwić użytkownikom usługi Azure AD logowanie się do kontrolki Workspot, muszą one być przydzielone do kontrolki Workspot. Inicjowanie obsługi administracyjnej jest zadaniem ręcznym.

**Aby zainicjować obsługę administracyjną konta użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do kontroli Workspot jako administrator zabezpieczeń.

2. Na pasku narzędzi u góry strony wybierz pozycję **Użytkownicy** , a następnie **Dodaj użytkownika**.

    ![Opcje "Użytkownicy"](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_adduser.png)

3. W oknie **Dodawanie nowego użytkownika** wykonaj następujące kroki:

    ![Okno "Dodawanie nowego użytkownika"](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addnewuser.png)

    1. W polu **imię i nazwisko** Wprowadź imię użytkownika, na przykład **Britta**.

    1. W polu tekstowym **nazwisko** Wprowadź nazwisko użytkownika, na przykład **Simon**.

    1. W polu **adres e-mail** wprowadź adres e-mail użytkownika, na przykład ** Brittasimon@contoso . <i></i> Model com**.

    1. Wybierz odpowiednią rolę użytkownika z listy rozwijanej **rola** .

    1. Wybierz odpowiednią grupę użytkowników z listy rozwijanej **Grupa** .

    1. Wybierz pozycję **Dodaj użytkownika**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji testuje konfigurację logowania jednokrotnego usługi Azure AD za pomocą *panelu dostępu*.

Po kliknięciu kafelka **Workspot** w panelu dostępu należy automatycznie zalogować się do formantu Workspot, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Samouczki dotyczące integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

- [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
