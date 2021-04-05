---
title: 'Samouczek: Azure Active Directory integrację z schematem organizacyjnym teraz | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i schematem organizacyjnym.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: fc6b90be8e951c0e30c99d0bb16f85e49cf991f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92522329"
---
# <a name="tutorial-azure-active-directory-integration-with-orgchart-now"></a>Samouczek: Azure Active Directory integrację z schematem organizacyjnym teraz

W tym samouczku dowiesz się, jak zintegrować schemat organizacyjny teraz z Azure Active Directory (Azure AD).
Integracja schematu organizacyjnego z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma teraz dostęp do schematu organizacyjnego.
* Możesz umożliwić użytkownikom automatyczne logowanie do schematu organizacyjnego teraz (Logowanie jednokrotne) za pomocą kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby teraz skonfigurować integrację usługi Azure AD z schematem organizacyjnym, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja z włączonym logowaniem jednokrotnym w ramach schematu organizacyjnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Schemat organizacyjny obsługuje teraz usługę **SP** i **dostawcy tożsamości** zainicjowane przez usługę SSO

## <a name="adding-orgchart-now-from-the-gallery"></a>Dodawanie schematu organizacyjnego teraz z galerii

Aby teraz skonfigurować integrację schematu organizacyjnego z usługą Azure AD, musisz teraz dodać schemat organizacyjny z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać teraz schemat organizacyjny z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **teraz schemat organizacyjny**, wybierz pozycję **schemat organizacyjny teraz** w panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

     ![Schemat organizacyjny teraz na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą schematu organizacyjnego teraz na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w infrastrukturze organizacyjnej.

Aby teraz skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą schematu organizacyjnego, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj teraz Logowanie jednokrotne schematu organizacyjnego](#configure-orgchart-now-single-sign-on)** — aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz teraz schemat organizacyjny, aby przetestować użytkownika](#create-orgchart-now-test-user)** jako odpowiednik Britta Simon w schemat organizacyjny, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby teraz skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą schematu organizacyjnego, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja z aplikacją **schemat organizacyjny teraz** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **dostawcę tożsamości**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następującą czynność:

    ![Schemat organizacyjny teraz domeny i adresy URL logowania jednokrotnego](common/idp-identifier.png)

    W polu tekstowym **Identyfikator** wpisz adres URL: `https://sso2.orgchartnow.com`

5. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    ![image (obraz)](common/both-preintegrated-signon.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://sso2.orgchartnow.com/Shibboleth.sso/Login?entityID=<YourEntityID>&target=https://sso2.orgchartnow.com`

    > [!NOTE]
    > `<YourEntityID>` czy **Identyfikator usługi Azure AD** został skopiowany z sekcji **Konfigurowanie schematu organizacyjnego teraz** , opisanej w dalszej części samouczka.

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

7. W sekcji **Skonfiguruj teraz schemat organizacyjny** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-orgchart-now-single-sign-on"></a>Skonfiguruj teraz pojedynczy Sign-On schematu organizacyjnego

Aby skonfigurować Logowanie jednokrotne w obszarze **schemat organizacyjny teraz** , należy wysłać pobrane **XML metadanych Federacji** i odpowiednie skopiowane adresy URL z Azure Portal do [schematu organizacyjnego teraz obsługiwać zespół](mailto:ocnsupport@officeworksoftware.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon@yourcompanydomain.extension**  
    Na przykład BrittaSimon@contoso.com

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

Ta sekcja umożliwia włączenie usługi Britta Simon w celu korzystania z logowania jednokrotnego platformy Azure przez przyznanie dostępu do schematu organizacyjnego teraz.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **schemat organizacyjny teraz**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **schemat organizacyjny teraz**.

    ![Łącze schemat organizacyjny teraz na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-orgchart-now-test-user"></a>Utwórz ponownie schemat organizacyjny dla użytkownika testowego

Aby umożliwić użytkownikom usługi Azure AD logowanie do schematu organizacyjnego teraz, muszą one być już obsługiwane w schematach organizacyjnych. 

1. Schemat organizacyjny obsługuje teraz Inicjowanie obsługi just in Time, która jest domyślnie włączona. Nowy użytkownik zostanie utworzony podczas próby uzyskania dostępu do schematu organizacyjnego teraz, jeśli jeszcze nie istnieje. Funkcja aprowizacji użytkowników just in Time utworzy tylko użytkownika **tylko do odczytu** , gdy żądanie rejestracji jednokrotnej pochodzi ze ROZPOZNAnego dostawcy tożsamościu, a wiadomość e-mail w potwierdzeniu SAML nie zostanie znaleziona na liście użytkowników. W przypadku tej funkcji samoobsługowego udostępniania musisz teraz utworzyć grupę dostępu zatytułowaną **Ogólne** w obszarze schemat organizacyjny. Wykonaj następujące kroki, aby utworzyć grupę dostępu:

    a. Po kliknięciu **koła zębatego** w prawym górnym rogu interfejsu użytkownika przejdź do opcji **Zarządzaj grupami** .

    ![Teraz grupy schematów organizacyjnych](./media/orgchartnow-tutorial/tutorial_orgchartnow_manage.png)    

    b. Wybierz ikonę **Dodaj** i nadaj grupie **Ogólne ustawienia** , a następnie kliknij przycisk **OK**. 

    ![Teraz dodano schemat organizacyjny](./media/orgchartnow-tutorial/tutorial_orgchartnow_add.png)

    c. Wybierz foldery, które mają być dostępne dla użytkowników ogólnych lub tylko do odczytu:

    ![Foldery organizacyjne teraz](./media/orgchartnow-tutorial/tutorial_orgchartnow_chart.png)

    d. **Zablokuj** foldery, aby tylko Administratorzy mogli je modyfikować. Następnie naciśnij przycisk **OK**.

    ![Schemat organizacyjny jest teraz zablokowany](./media/orgchartnow-tutorial/tutorial_orgchartnow_lock.png)

2. Aby utworzyć użytkowników **administratorów** i użytkowników **odczytu/zapisu** , musisz ręcznie utworzyć użytkownika w celu uzyskania dostępu do ich poziomu uprawnień za pomocą logowania jednokrotnego. Aby aprowizować konto użytkownika, wykonaj następujące czynności:

    a. Zaloguj się do schematu organizacyjnego teraz jako administrator zabezpieczeń.

    b.  Kliknij pozycję **Ustawienia** w prawym górnym rogu, a następnie przejdź do obszaru **Zarządzanie użytkownikami**.

    ![Ustawienia schematu organizacyjnego teraz](./media/orgchartnow-tutorial/tutorial_orgchartnow_settings.png)

    c. Kliknij pozycję **Dodaj** i wykonaj następujące czynności:

    ![Schemat organizacyjny teraz zarządza](./media/orgchartnow-tutorial/tutorial_orgchartnow_manageusers.png)

    * W polu tekstowym **Identyfikator użytkownika** wprowadź identyfikator użytkownika, taki jak **brittasimon \@ contoso.com**.

    * W polu tekstowym **adres e-mail** wprowadź wiadomości e-mail użytkownika, takie jak **brittasimon \@ contoso.com**.

    * Kliknij pozycję **Dodaj**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka schemat organizacyjny teraz w panelu dostępu należy automatycznie zalogować się do schematu organizacyjnego, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)